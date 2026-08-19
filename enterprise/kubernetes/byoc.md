---
description: Connect a Kubernetes cluster in your own cloud account to Spice.ai Cloud with the operator's managed mode.
icon: cloud
---

# Bring-Your-Own-Cluster (BYOC)

Bring-Your-Own-Cluster (BYOC) connects a Kubernetes cluster that runs in **your own cloud account** to Spice.ai Cloud. You install the Spice.ai Kubernetes Operator in your cluster and enable **managed mode**. Spice.ai Cloud then deploys and observes Spicepods in your cluster, while your data stays in your account.

{% hint style="info" %}
BYOC is available on the **Enterprise plan**. Registering, decommissioning, and issuing enrollment tokens for a cluster requires the organization **Admin** or **Owner** role.
{% endhint %}

## How managed mode works

Managed mode is a remote-control channel, not a data channel:

- The operator opens **one outbound connection** from your cluster to the Spice.ai Cloud gateway. It is a long-lived, bidirectional gRPC stream secured with mutual TLS (mTLS).
- Spice.ai Cloud **does not open any inbound connection** to your cluster. You do not expose an endpoint, open a firewall port, or share cluster credentials.
- The operator stays the **source of truth** for the `SpicepodSet` and `SpicepodCluster` resources in your cluster. Spice.ai Cloud sends commands (apply, delete, pause, drain, get status, get logs) over the stream; the operator applies them locally.
- Query traffic and data **do not** flow through the stream. The stream carries only commands, command results, heartbeats, telemetry roll-ups, and metrics.

The operator makes three kinds of outbound connections:

| Purpose             | Protocol            | Destination                                                    | Port |
| ------------------- | ------------------- | -------------------------------------------------------------- | ---- |
| Enrollment (once)   | HTTPS `POST`        | `https://api.spice.ai/v1/cloud-connect/enroll`                 | 443  |
| Identity renewal    | HTTPS `POST`        | `https://api.spice.ai/v1/cloud-connect/renew`                  | 443  |
| Control stream      | gRPC over mTLS      | `connect.<region>-aws.spiceai.io` (assigned during enrollment) | 443  |

The cluster also pulls the operator chart and the Spice runtime images from the AWS Marketplace registry, `709825985650.dkr.ecr.us-east-1.amazonaws.com`. Allow egress to all of these hosts on port 443.

### Identity lifecycle

The operator gets its identity by **enrollment**:

1. At install, the operator holds only a single-use **enrollment token** (`spice-enroll-…`) that you mint in the Spice.ai Cloud portal.
2. On first start, the operator generates a private key and a certificate signing request (CSR) locally. It sends the CSR and the token to the enrollment endpoint. The private key never leaves the cluster.
3. Spice.ai Cloud returns a signed mTLS certificate, the CA bundle, and the gateway address. The operator stores them in a Secret it owns (`spice-byoc-operator-identity` by default). **Do not create or edit this Secret yourself.**
4. The operator connects to the gateway with the new identity. Each later restart reuses the stored identity; enrollment happens only once.

The identity certificate is valid for **24 hours**. The operator renews it automatically at half-life (about every 12 hours) with a proof-of-possession request, and rotates its keys on every renewal. If renewal fails, a **30-day grace period** applies during which renewal still works. Past the grace period, or when Spice.ai Cloud terminally rejects the identity (for example, after you decommission the cluster), the operator stops managed mode and needs a fresh enrollment token.

## Prerequisites

- A Spice.ai Cloud organization on the **Enterprise plan**, and the **Admin** or **Owner** role in it.
- An AWS account subscribed to the [AWS Marketplace](../deployment/aws-marketplace.md) Spice.ai Enterprise listing. Accounts that are not subscribed can log in to the registry but the image pull is refused.
- A Kubernetes cluster (1.33+) with storage and IAM already provisioned — Spice.ai Cloud does not create cloud infrastructure in your account.
- `kubectl`, `helm` (3.x; 3.17+ recommended for versioned `oci://` chart references), and the `aws` CLI.
- Egress to the endpoints listed above on port 443.
- Operator chart version `1.1.0-operator-helm` or later (the first release with BYOC support).

## Step 1: Register the cluster in Spice.ai Cloud

1. Open the [Spice.ai Cloud portal](https://spice.ai) and go to **Clusters → BYOC**.
2. Select **Add a cluster**.
3. On the **Details** step, enter:
   - **Cluster name** — for example `production-us-east`.
   - **AWS account ID** (optional) — the 12-digit account the cluster runs in.
   - **AWS region** — any AWS region. One cluster per AWS account + region pair; multi-region clusters are not yet supported.
4. Select **Continue**. The portal creates the cluster record and shows the **Launch in AWS** step with:
   - The **enrollment token** (`spice-enroll-…`).
   - The **cluster ID** and region.
   - A ready-to-run Helm command.

{% hint style="warning" %}
**Copy the enrollment token now — it cannot be retrieved later.** The token is shown once, is single-use, and expires after **1 hour** by default. Anyone with the token can register an operator as this cluster, so treat it like a secret. If the token expires or is lost before the operator enrolls, issue a new one from the cluster's **Setup** tab (this revokes the old token).
{% endhint %}

Alternatively, create a cluster and token through the management API: `POST /v1/cloud-connect/token` with `{ "kind": "cluster", "name": "...", "region": "..." }`, authenticated as an organization admin with the `clusters:write` scope.

## Step 2: Install the operator with managed mode

Authenticate Helm to the AWS Marketplace registry, then install the operator with managed mode enabled. Read the token into an environment variable first so it does not enter your shell history:

```bash
read -s SPICE_ENROLLMENT_TOKEN   # paste the enrollment token

aws ecr get-login-password --region us-east-1 | \
  helm registry login --username AWS --password-stdin \
  709825985650.dkr.ecr.us-east-1.amazonaws.com

helm upgrade --install spiceai-operator \
  oci://709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/spiceai-enterprise-plan \
  --version 1.1.0-operator-helm \
  --namespace spiceai-operator-system --create-namespace \
  --set spice.managedMode.enabled=true \
  --set-string spice.managedMode.enrollmentToken="$SPICE_ENROLLMENT_TOKEN"
```

{% hint style="info" %}
A value passed through `spice.managedMode.enrollmentToken` is persisted in Helm's release history in plaintext. The token is single-use and short-lived, which limits the exposure — but for production, pre-create the Secret yourself and reference it with `enrollmentTokenSecret` so the token never enters Helm state:

```bash
kubectl create namespace spiceai-operator-system
kubectl -n spiceai-operator-system create secret generic spice-enrollment-token \
  --from-literal=token="$SPICE_ENROLLMENT_TOKEN"

helm upgrade --install spiceai-operator \
  oci://709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/spiceai-enterprise-plan \
  --version 1.1.0-operator-helm \
  --namespace spiceai-operator-system \
  --set spice.managedMode.enabled=true \
  --set spice.managedMode.enrollmentTokenSecret=spice-enrollment-token
```
{% endhint %}

When managed mode is disabled (the default), the operator behavior does not change: it opens no outbound stream, and every `spice.managedMode.*` value is ignored. Existing self-hosted installations are unaffected.

## Step 3: Verify the connection

Enrollment and connection normally complete within a few minutes. Check the operator logs:

```bash
kubectl -n spiceai-operator-system logs deploy/spiceai-operator | grep -i "managed mode"
```

A healthy first start logs, in order:

```
Managed mode enrolling over the cloud /enroll endpoint
Managed mode enrollment complete; identity persisted to Secret 'spice-byoc-operator-identity'
Managed mode connected to Spice Cloud gateway
```

In the portal, the wizard's **Connect cluster** step polls automatically and reports **Cluster is online**. You can safely close the page while you wait; the cluster detail page shows registration errors on its **Events** tab.

### Cluster status reference

The portal shows one of these statuses for each BYOC cluster:

| Status              | Meaning                                                                                             |
| ------------------- | --------------------------------------------------------------------------------------------------- |
| **Pending**         | The cluster is registered, but no operator has connected yet.                                        |
| **Online**          | The operator has a live connection to the gateway.                                                   |
| **Degraded**        | The operator is reachable, but reports a partial failure (for example, unhealthy runtime instances). |
| **Offline**         | The operator's heartbeat was lost.                                                                   |
| **Decommissioning** | Decommission was requested; the cluster is shutting down its connection.                             |
| **Archived**        | Teardown is complete. The cluster is kept for audit and hidden from the clusters list.                |

## Container resource metrics (kubelet)

In managed mode, the operator collects two metric streams from each managed pod and forwards them to Spice.ai Cloud:

1. **Spice runtime metrics** — scraped from each pod's own `/metrics` endpoint.
2. **Container CPU and memory** — scraped from the `/metrics/resource` endpoint of the **kubelet** on each node. These values power the resource graphs in the portal.

The kubelet scrape does not use the operator's own credentials. The chart creates a dedicated ServiceAccount, `spiceai-operator-kubelet-metrics`, whose only permission is `get nodes/metrics`. The operator mints short-lived tokens for that ServiceAccount and presents them to the kubelets. This keeps the credential that reaches the nodes as narrow as possible.

### Kubelet TLS verification

The kubelet serves `/metrics/resource` over HTTPS. By default, the operator **verifies** the kubelet's serving certificate against the cluster CA and refuses to scrape a kubelet it cannot verify. Many clusters — including default **EKS, GKE, and AKS** clusters — use self-signed kubelet serving certificates that fail this check. To find out which case applies to your cluster, run:

```bash
kubectl get csr | grep kubelet-serving
```

- **Approved requests appear** — your cluster issues kubelet certificates from its own CA. The default configuration works; set neither option below.
- **No output** — your kubelets self-sign. Container CPU/memory collection needs one of these two options (set at most one):

| Option                              | Helm value                              | Behavior                                                                                                                             |
| ----------------------------------- | ---------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| Supply the kubelet CA (preferred)   | `spice.managedMode.kubeletCaSecret`      | Name of a Secret that holds the CA which signs kubelet serving certificates, under the key `ca.crt`. The operator verifies against it. |
| Skip verification                   | `spice.managedMode.kubeletInsecureTls`   | `true` disables certificate verification for the kubelet scrape only.                                                                 |

These options mirror `metrics-server`'s `--kubelet-certificate-authority` and `--kubelet-insecure-tls` — it is the same endpoint and the same problem.

{% hint style="warning" %}
With `kubeletInsecureTls: true`, the scrape presents its metrics-only token to whatever answers on the node's address, without proof it is the kubelet. That token can only read node metrics — nothing else — but prefer `kubeletCaSecret` wherever the cluster can provide a CA. The operator logs a warning at startup while verification is disabled.
{% endhint %}

If neither option is set and the kubelet certificates cannot be verified, the operator logs the reason at startup and counts each skipped scrape on the metric `spiceai_operator_managed_metrics_scrape_total{source="kubelet",result="tls_error"}`. Spice runtime metrics keep flowing regardless — only the container CPU/memory graphs stay empty.

## Configuration reference

### Helm values

All managed-mode values live under `spice.managedMode`:

| Value                       | Default                        | Description                                                                                                                          |
| --------------------------- | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------ |
| `enabled`                   | `false`                        | Master switch. When `false`, no outbound stream is opened and every other value here is ignored.                                       |
| `enrollmentToken`           | —                              | The single-use `spice-enroll-…` token from the portal. The chart stores it in a Secret for the operator. Persisted in Helm history — see Step 2. |
| `enrollmentTokenSecret`     | —                              | Name of an existing Secret that holds the enrollment token in its `token` data key. Use instead of `enrollmentToken` in production. When the chart provisions the Secret from `enrollmentToken`, it is named `spice-enrollment-token`. |
| `enrollUrl`                 | `https://api.spice.ai/v1/cloud-connect` | Base URL of the enrollment API. Leave empty for Spice.ai Cloud; set only for a self-hosted control plane.                    |
| `endpoint`                  | —                              | Gateway address override. Not needed for enrollment — the gateway address comes from the enrollment response. Required only with a pre-provisioned identity. |
| `identitySecret`            | `spice-byoc-operator-identity` | Name of the Secret the operator **creates** to persist its enrolled identity. Operator-owned — do not pre-create or edit it.           |
| `secretNamespace`           | (operator namespace)           | Namespace for the managed-mode Secrets. Leave empty to use the operator's own namespace.                                               |
| `instanceId`                | —                              | Instance identifier. Normally empty — it is assigned during enrollment. Required only with a pre-provisioned identity.                 |
| `mtlsSecret`                | —                              | Pre-provisioned client identity (`tls.crt`/`tls.key`) — skips enrollment. Must be set together with `caSecret`.                        |
| `caSecret`                  | —                              | Pre-provisioned control-plane CA bundle (`ca.crt`). Must be set together with `mtlsSecret`.                                            |
| `serverCaSecret`            | —                              | Extra serving-CA trust anchor (`ca.crt`) for a private/self-hosted control plane. Trusted in addition to public roots.                 |
| `heartbeatIntervalSeconds`  | `30`                           | Heartbeat cadence.                                                                                                                     |
| `telemetryIntervalSeconds`  | `60`                           | Telemetry roll-up cadence.                                                                                                             |
| `metricsIntervalSeconds`    | `30`                           | Managed-instance metrics collection cadence.                                                                                           |
| `kubeletCaSecret`           | —                              | Secret with the CA that signs kubelet serving certificates (`ca.crt`). Mutually exclusive with `kubeletInsecureTls`.                    |
| `kubeletInsecureTls`        | `false`                        | Skip kubelet certificate verification. Mutually exclusive with `kubeletCaSecret`.                                                      |

### CLI flags

The Helm values map to `run` flags on the operator binary. Each flag also reads a matching `SPICEAI_MANAGED_MODE_*` environment variable (for example, `--managed-mode-enabled` reads `SPICEAI_MANAGED_MODE_ENABLED`).

| Flag                                          | Default                                  | Helm value                          |
| --------------------------------------------- | ---------------------------------------- | ----------------------------------- |
| `--managed-mode-enabled`                      | `false`                                  | `enabled`                           |
| `--managed-mode-enroll-url`                   | `https://api.spice.ai/v1/cloud-connect`  | `enrollUrl`                         |
| `--managed-mode-enrollment-token-secret`      | —                                        | `enrollmentTokenSecret`             |
| `--managed-mode-endpoint`                     | —                                        | `endpoint`                          |
| `--managed-mode-identity-secret`              | `spice-byoc-operator-identity`           | `identitySecret`                    |
| `--managed-mode-secret-namespace`             | (operator namespace)                     | `secretNamespace`                   |
| `--managed-mode-instance-id`                  | —                                        | `instanceId`                        |
| `--managed-mode-mtls-secret`                  | —                                        | `mtlsSecret`                        |
| `--managed-mode-ca-secret`                    | —                                        | `caSecret`                          |
| `--managed-mode-server-ca-path`               | —                                        | `serverCaSecret` (mounted as a file) |
| `--managed-mode-heartbeat-interval-seconds`   | `30`                                     | `heartbeatIntervalSeconds`          |
| `--managed-mode-telemetry-interval-seconds`   | `60`                                     | `telemetryIntervalSeconds`          |
| `--managed-mode-metrics-interval-seconds`     | `30`                                     | `metricsIntervalSeconds`            |
| `--managed-mode-kubelet-certificate-authority`| —                                        | `kubeletCaSecret` (mounted as a file) |
| `--managed-mode-kubelet-insecure-tls`         | `false`                                  | `kubeletInsecureTls`                |

### Resources created by managed mode

Enabling managed mode adds these objects, beyond the standard operator installation:

- **RBAC** — the operator ClusterRole gains `get` on `nodes` (to read each node's kubelet port) and permission to mint tokens for exactly one ServiceAccount. The chart also creates the `spiceai-operator-kubelet-metrics` ServiceAccount with a ClusterRole that grants `get nodes/metrics` and nothing else.
- **Secrets** — the operator creates and owns the identity Secret (`spice-byoc-operator-identity`). Secrets delivered from Spice.ai Cloud carry the label `spice.ai/managed-mode-secret: "true"`; the operator refuses to delete Secrets without that label.
- **ConfigMap** — `spice-managed-mode-pause` in the operator's namespace, used as a pause gate (see below).
- **Namespaces** — the operator creates the application namespace before it applies a manifest sent from Spice.ai Cloud, when that namespace does not exist yet.

Secret values sent from Spice.ai Cloud are sealed with two layers of public-key encryption (HPKE): one to a key that persists across restarts, and one to a key that exists only in the operator's memory for the current connection. There is no plaintext path.

### Pause behavior

Managed mode includes a **fail-closed pause gate** for the resources that Spice.ai Cloud manages. When you pause the cluster from the portal (**Admin → Pause**), the operator stops reconciling cloud-managed resources. The gate also starts in the paused state on every boot, until the operator reads the `spice-managed-mode-pause` ConfigMap.

Only resources applied through the cloud connection are affected. Resources you apply yourself with `kubectl` or Helm keep reconciling normally, even while the cluster is paused or disconnected.

{% hint style="warning" %}
If managed mode is enabled but the operator cannot reach Spice.ai Cloud at boot, cloud-managed resources stay paused until the connection succeeds. Self-applied resources are not affected.
{% endhint %}

## Pre-provisioned identity (advanced)

If you manage certificates out of band (for example, with cert-manager against a self-hosted control plane), you can skip enrollment and supply the identity directly. Set all of these together:

```yaml
spice:
  managedMode:
    enabled: true
    mtlsSecret: my-operator-identity   # kubernetes.io/tls Secret: tls.crt, tls.key
    caSecret: my-control-plane-ca      # Secret with ca.crt
    instanceId: my-instance-id
    endpoint: connect.example.com:443
```

With a pre-provisioned identity the operator does not enroll and does not run its renewal loop — your certificate issuer owns rotation. `enrollmentToken`, `enrollmentTokenSecret`, and `enrollUrl` are ignored on this path. For a control plane that serves a certificate from a private CA, additionally set `serverCaSecret` so enrollment and reconnects trust it; concatenate multiple CAs into one PEM bundle if needed.

## Operating a BYOC cluster

- **Deploy** — once the cluster is **Online**, it appears as a deploy target in the Spice.ai Cloud portal. In the new-app flow, choose **Bring your own cloud** and select the cluster.
- **Rotate the enrollment token** — from the cluster's **Setup** tab (before first enrollment) or **Admin** tab. Issuing a new token revokes the outstanding one. Tokens can only be rotated before the cluster enrolls.
- **Pause / resume** — from the cluster's **Admin** tab. Pause stops reconciliation of cloud-managed resources; resume restores it.
- **Decommission** — from the cluster's **Admin** tab, with a typed-name confirmation. The cluster moves to **Decommissioning**, its identity is terminally rejected, and the record is finally **Archived**. To connect the same Kubernetes cluster again, register a new BYOC cluster and enroll with a fresh token.
- **Re-enroll** — to force a fresh enrollment on an existing registration (for example, after restoring the operator from a broken state), delete the identity Secret and restart the operator with a new enrollment token:

  ```bash
  kubectl -n spiceai-operator-system delete secret spice-byoc-operator-identity
  ```

## Troubleshooting

Search the operator logs for `managed mode` first — every failure mode below logs a clear message. The cluster's **Events** tab in the portal shows the same lifecycle events (`managed-mode-enrolled`, `managed-mode-connected`, `managed-mode-disconnect`, `managed-mode-renewal-degraded`, and others).

### `helm install` fails

| Message contains                                                             | Cause and fix                                                                                                                             |
| ---------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| `bootstrapToken was replaced by spice.managedMode.enrollmentToken`           | The values use the old pre-release key names. Rename `bootstrapToken` → `enrollmentToken` and `bootstrapTokenSecret` → `enrollmentTokenSecret`. |
| `requires either enrollmentToken/enrollmentTokenSecret … or both mtlsSecret and caSecret` | `enabled: true` was set with no identity source. Provide an enrollment token, or a full pre-provisioned identity.               |
| `mtlsSecret and caSecret must be set together`                               | Half of a pre-provisioned identity. Set both, or neither.                                                                                    |
| `instanceId is required with a pre-provisioned identity`                     | Add `instanceId` (and `endpoint`) when using `mtlsSecret` + `caSecret`.                                                                      |
| `kubeletInsecureTls and kubeletCaSecret are mutually exclusive`              | Set at most one of the two kubelet options.                                                                                                  |
| Chart pull is refused                                                        | The AWS identity is not subscribed to the Marketplace listing, or the ECR login expired. Subscribe, then re-run `helm registry login`.       |

### Operator pod does not start

The operator **fails hard at startup** when the managed-mode configuration is invalid, instead of running in a wedged state. The log names the exact flag:

```
managed mode failed to start: managed mode configuration invalid: ...
```

Fix the value named in the message and roll the deployment. If the pod crash-loops with a `clap` argument-parsing error (for example, `unexpected value 'true'`), the operator **image** is older than the chart — align `image.tag` with the chart version.

### Enrollment fails

| Symptom in logs                                                             | Cause                                                                       | Fix                                                                                     |
| ---------------------------------------------------------------------------- | ---------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| `failed to load managed-mode enrollment token secret '<name>' in namespace '<ns>'` | The Secret does not exist, or `secretNamespace` points at the wrong namespace. | Create the Secret in the right namespace, or fix `secretNamespace`.                    |
| `is missing required key 'token'`                                            | The Secret exists but the data key is wrong.                                  | Store the token under the data key `token`.                                              |
| `does not start with 'spice-enroll-'` / `expected exactly 32 characters after 'spice-enroll-'` | The token was mistyped, truncated, or is not an enrollment token.       | Copy the token exactly as the portal shows it.                                          |
| Enroll endpoint returns `410`                                                 | The token **expired** (1-hour default lifetime). This is the most common first-run failure. | Issue a new token from the cluster's **Setup** tab and update the Secret.  |
| Enroll endpoint returns `409` (consumed)                                      | The token was already used, or the cluster already enrolled once.             | Issue a new token. If a previous operator install enrolled, delete the identity Secret first, or decommission and re-register. |
| Enroll endpoint returns `401`                                                 | The token was revoked (a newer token was issued) or is unknown.               | Use the most recently issued token.                                                      |
| `Managed mode identity/enrollment failed: …; retrying`                        | Transient network or control-plane error; the operator retries with backoff.  | Check egress to `api.spice.ai:443`. The operator recovers on its own once reachable.     |

### Connection drops or never comes up

| Symptom in logs                                                             | Cause and fix                                                                                                                              |
| ---------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| `Managed mode connection failed: …; reconnecting`                            | Transient. The operator reconnects with backoff (1s doubling to a 60s cap). Persistent repeats usually mean egress to `connect.<region>-aws.spiceai.io:443` is blocked. |
| `Spice Cloud stream closed unexpectedly; reconnecting with backoff`          | The cloud side closed the stream (for example, a gateway restart). The operator reconnects automatically.                                     |
| `connection persistently rejected with an expired identity; … Stopping managed mode` | The cluster was decommissioned, or the identity is past recovery. This is a hard stop. Register/rotate in the portal, delete the identity Secret, and enroll with a fresh token. |
| `failed to build TLS client: reading server_ca_path`                         | The `serverCaSecret` mount is missing or holds invalid PEM. Fix the Secret; the operator deliberately does not fall back to public roots.      |

### Renewal warnings

| Symptom in logs                                                             | Meaning and action                                                                                                                          |
| ---------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| `Managed mode identity renewal failed: …; backing off`                       | A single failed attempt. No action needed; the operator retries (5s doubling to a 5-minute cap).                                              |
| `identity renewal is failing repeatedly; the identity expires if this is not resolved` | Renewal has failed for 3 consecutive attempts or 5 minutes. Check egress to `api.spice.ai:443`. The log includes `expires_in_hours`; there is a 30-day grace period after expiry. |
| `identity renewal recovered`                                                 | The alert condition cleared. No action needed.                                                                                                |
| `renewal terminally rejected … a fresh enrollment is required`               | Spice.ai Cloud rejected the identity permanently (decommissioned or past grace). Delete the identity Secret and enroll with a fresh token.     |
| `identity secret … carries mTLS material but a missing or empty 'identifier'` | The identity Secret was edited or partially restored. Delete it to force re-enrollment, as the message says.                                  |

### Container CPU/memory graphs are empty

Check the scrape counter on the operator's Prometheus endpoint (see [Operator Metrics](metrics.md)):

```bash
kubectl -n spiceai-operator-system port-forward deploy/spiceai-operator 9090:9090
curl -s http://localhost:9090/metrics | grep managed_metrics
```

| `result` label on `…managed_metrics_scrape_total{source="kubelet"}` | Cause and fix                                                                                                                          |
| -------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| `tls_error`                                                           | The kubelet serving certificate cannot be verified — the common case on EKS/GKE/AKS. Set `kubeletCaSecret` or `kubeletInsecureTls: true` (see [Kubelet TLS verification](#kubelet-tls-verification)). |
| `no_client`                                                           | `kubeletCaSecret` points at an unreadable or empty CA. The startup log names the reason.                                                  |
| `forbidden`                                                           | The token authenticated, but authorization failed — the `spiceai-operator-kubelet-metrics` ClusterRole or binding is missing. Reinstall or repair the chart RBAC. |
| `unauthorized`                                                        | The kubelet could not authenticate the token at all. Check that kubelet webhook authentication is enabled on the nodes.                    |
| `no_scraper_token`                                                    | The operator cannot mint a token for `spiceai-operator-kubelet-metrics` — the ServiceAccount or the `serviceaccounts/token` grant is missing. Reinstall or repair the chart RBAC. |
| `missing_identity` (on `source="discovery"`)                          | Managed pods lack the annotations that identify them to Spice.ai Cloud. Pods deployed from the portal carry them automatically.            |

Spice runtime metrics (`source="spiced"`) and kubelet metrics are independent — a kubelet TLS problem never blocks runtime metrics.

### Cluster stays **Pending** in the portal

The operator never connected. Work through, in order: the enrollment token was copied within its lifetime (see [Enrollment fails](#enrollment-fails)); the operator pod is running; the logs show `Managed mode connected to Spice Cloud gateway`; egress on 443 to `api.spice.ai` and `connect.<region>-aws.spiceai.io` is allowed.

### Cluster was Online and is now **Offline**

The heartbeat stopped. Check that the operator pod is running and that egress to the gateway is still allowed. The operator reconnects automatically; the status returns to **Online** on the next successful heartbeat. If the cluster was decommissioned in the portal, the operator logs a terminal rejection instead — see [Connection drops](#connection-drops-or-never-comes-up).

## See also

- [Overview](README.md) — installation, all Helm values, and CLI flags.
- [User Guide](user-guide.md) — deploying and operating Spicepods with the operator.
- [Operator Metrics](metrics.md) — the operator's own Prometheus/OTLP telemetry, including managed-mode scrape counters.
- [AWS Marketplace](../deployment/aws-marketplace.md) — subscribing and authenticating to the registry.
