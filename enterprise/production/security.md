---
description: Security hardening checklist for production Spice.ai Enterprise deployments.
icon: lock
---

# Security

Spice.ai Enterprise ships secure-by-default \u2014 mTLS between cluster nodes, non-root container UID, scratch base image, OIDC authentication. This page documents the additional hardening steps every production deployment should apply.

For protocol-level details, see [Authentication](../features/authentication.md) and [mTLS Cluster Security](../features/mtls.md).

## Container and pod security

### Image provenance

- Pull Spice.ai Enterprise runtime and operator images only from the official **AWS Marketplace ECR registry**. The images are not published to a public registry. See [AWS Marketplace](../deployment/aws-marketplace.md) for subscription and pull setup.
- Pin every image reference to an **immutable digest** rather than a floating tag:

```yaml
spec:
  spiceai_image_registry: 709825985650.dkr.ecr.us-east-1.amazonaws.com
  spiceai_image_name: spice-ai/spiceai-enterprise-byol
  spiceai_image_tag: 2.3.0-enterprise-models@sha256:1f4a...
```

- Verify image signatures with `cosign verify` against the Spice.ai public key prior to admission. Wire this into the cluster's image policy webhook (Kyverno, OPA Gatekeeper, AWS Signer).
- Run the organization's image scanner (Trivy, Snyk, Aqua) against every image before promotion. Spice publishes SBOMs for every Enterprise image.

### Pod security

The operator and the Helm chart already configure non-root execution. Layer in the rest of the [Pod Security Standards "restricted" profile](https://kubernetes.io/docs/concepts/security/pod-security-standards/#restricted) on the namespace:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: spiceai
  labels:
    pod-security.kubernetes.io/enforce: restricted
    pod-security.kubernetes.io/enforce-version: latest
```

The runtime image is `FROM scratch` and runs as UID `65534` (`nobody`). Production deployments should additionally:

```yaml
spec:
  podSecurityContext:
    fsGroup: 65534
    seccompProfile:
      type: RuntimeDefault
  securityContext:
    runAsNonRoot: true
    runAsUser: 65534
    allowPrivilegeEscalation: false
    readOnlyRootFilesystem: true
    capabilities:
      drop: ["ALL"]
```

When `readOnlyRootFilesystem: true` is set, mount an `emptyDir` at `/tmp` for the runtime's scratch space.

## Network policy

Limit ingress to the load balancer / ingress controller and egress to the upstream data sources Spice actually needs:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: spiceai-default
  namespace: spiceai
spec:
  podSelector:
    matchLabels:
      spice.ai/app: prod-router
  policyTypes: [Ingress, Egress]
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              name: ingress-nginx
      ports:
        - { port: 8090, protocol: TCP }
        - { port: 50051, protocol: TCP }
  egress:
    - to:
        - namespaceSelector: {}
      ports:
        - { port: 53, protocol: UDP }
    - to:
        - ipBlock: { cidr: 10.0.0.0/8 }   # Internal VPC
      ports:
        - { port: 443, protocol: TCP }
```

For `SpicepodCluster`, additionally allow scheduler/executor traffic on `50051` (Flight) and `50052` (cluster mTLS) within the cluster namespace.

The Spice Kubernetes Operator also accepts `egress` and `ingress` on the `SpicepodSet` spec directly:

```yaml
spec:
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              name: api-gateway
      ports:
        - { port: 8090, protocol: TCP }
  egress:
    - to:
        - ipBlock: { cidr: 10.10.0.0/16 }
      ports:
        - { port: 5432, protocol: TCP }   # Postgres data source
```

## Authentication

Production deployments must authenticate every external request. Spice.ai supports:

- [**OIDC bearer tokens**](../features/authentication.md#oidc) \u2014 JWTs issued by Microsoft Entra ID, Okta, Auth0, AWS Cognito, etc.
- [**API keys**](../features/authentication.md#api-keys) \u2014 hashed keys configured in the runtime, suitable for service-to-service calls.
- **Combined** \u2014 OIDC for human users, API keys for service callers, both checked on every request.

The runtime exposes the authenticated principal in SQL via the [identity functions](../features/authentication.md#identity-sql-functions): `current_user_id()`, `current_org_id()`, `current_user_has_role('<role>')`, and `session_property('<key>')`. Use these in row filters and column masks to enforce per-user authorization.

For allow/deny decisions across datasets, models, tools, and endpoints — plus fine-grained [row filters and column masks](../features/policy.md#row-filters-and-column-masks) — configure [Cedar-based authorization policy](../features/policy.md) under `runtime.authorization`.

{% hint style="warning" %}
Never expose the runtime externally without authentication. The default Helm chart configuration permits all callers \u2014 always set `auth.enabled: true` and configure at least one provider for production.
{% endhint %}

## Secrets management

Secrets should never be embedded in `values.yaml` or in checked-in Spicepods. Source them from a [secret store](https://spiceai.org/docs/components/secret-stores):

| Platform | Recommended store                                                                    |
| -------- | ------------------------------------------------------------------------------------ |
| AWS      | [AWS Secrets Manager](https://spiceai.org/docs/components/secret-stores/aws-secrets-manager) with IRSA. |
| Azure    | [Azure Key Vault](https://spiceai.org/docs/components/secret-stores/azure-keyvault) with workload identity. |
| GCP      | Secret Manager via the GKE workload identity binding.                                |
| Any      | [Kubernetes Secrets](https://spiceai.org/docs/components/secret-stores/kubernetes) (supports External Secrets Operator). |
| Local    | [Keyring](https://spiceai.org/docs/components/secret-stores/keyring) for development only. |

For Kubernetes deployments, prefer the External Secrets Operator or SOPS-encrypted manifests in Git; both provide auditability without checked-in plaintext.

## mTLS for `SpicepodCluster`

`SpicepodCluster` provisions a self-signed root CA and issues per-node leaf certificates automatically. Production checklist:

- [ ] `allowInsecureConnections` is **not** set on the `SpicepodCluster` (default).
- [ ] An alert on `spiceai_operator_cluster_ca_expiry_timestamp_seconds - time() < 7 * 24 * 3600` is wired up. See [Observability](observability.md#alerts).
- [ ] The CA secret backup procedure is documented \u2014 loss of the CA forces a full cluster certificate re-issuance.

For deployments that require a customer-managed CA (for example, an enterprise PKI or HashiCorp Vault), issue the root CA externally and pass it to the operator via the [mTLS configuration](../features/mtls.md).

## Audit logging

Spice's structured logs include the authenticated principal on every query and admin operation. Forward these to the organization's SIEM:

- Tag query logs with `tenant`, `principal`, and `dataset` labels for downstream filtering.
- Retain authentication failures for at least 90 days.
- Alert on bursts of `Unauthorized` / `Forbidden` HTTP status codes per source IP.

## Operator RBAC

The Spice Kubernetes Operator runs with the minimum permissions it needs to manage `SpicepodSet` and `SpicepodCluster` resources. The Helm chart's `ClusterRole` is intentionally scoped \u2014 do not extend it without review.

If running multiple Spice Operators in a single cluster (for multi-tenant isolation), use the `--watch-namespace` flag to scope each operator to a single namespace and grant a namespaced `Role` rather than a `ClusterRole`.

## Hardening checklist

- [ ] Image references pinned to immutable digests.
- [ ] Image signatures verified via `cosign` admission policy.
- [ ] Image scan results reviewed before promotion.
- [ ] Pod Security Standards `restricted` profile enforced on the namespace.
- [ ] `runAsNonRoot`, `readOnlyRootFilesystem`, dropped capabilities, RuntimeDefault seccomp.
- [ ] `NetworkPolicy` restricting ingress and egress.
- [ ] OIDC or API key authentication enabled and tested.
- [ ] Secrets sourced from a secret store, not `values.yaml`.
- [ ] Identity SQL functions used for row-level authorization where applicable.
- [ ] [Cedar authorization policy](../features/policy.md) is enabled with `default: deny` and an audited admin allow rule.
- [ ] mTLS enabled on every `SpicepodCluster`; certificate expiry alert wired.
- [ ] Audit logs forwarded to SIEM with 90-day retention.
- [ ] Operator RBAC reviewed; namespace-scoped operators for multi-tenant clusters.
