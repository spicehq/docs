---
description: Versioning, rolling upgrades, and rollback procedures for Spice.ai Enterprise.
icon: arrow-up-right-dots
---

# Upgrades

Production Spice.ai Enterprise deployments upgrade three components independently:

1. **Spice Kubernetes Operator** \u2014 the controller that reconciles `SpicepodSet` and `SpicepodCluster`.
2. **Spice runtime image** \u2014 referenced from each `SpicepodSet` / `SpicepodCluster`.
3. **CRDs** \u2014 typically bundled with the operator chart but updatable separately.

This page describes the supported version skew, the recommended upgrade order, and rollback procedures.

## Versioning

Spice.ai Enterprise follows [Semantic Versioning](https://semver.org/). The runtime image, the operator, and the CRDs are all released on the same version line.

| Tier        | Branch                       | Update cadence                | Support window      |
| ----------- | ---------------------------- | ----------------------------- | ------------------- |
| **Latest**  | Most recent stable release.  | New features and security patches. | Always supported.    |
| **N-1**     | Previous minor release.      | Security and critical fixes only.  | Supported until N+1. |
| **LTS**     | Designated long-term tag.    | Security patches only.             | Up to 3 years.       |

Subscribe to release announcements in the Enterprise customer Slack and the [Spice.ai release notes](https://spiceai.org/blog).

### Version skew policy

| Skew                                        | Supported? |
| ------------------------------------------- | ---------- |
| Operator version newer than runtime version | Yes \u2014 the operator's CRDs must be at least as new as the runtime expects. |
| Runtime version newer than operator version | Limited. Patch-level skew is supported; minor-level skew is not.            |
| CRDs newer than operator                    | No.       |
| CRDs older than operator                    | Limited. Patch-level skew is supported.                                     |

Always upgrade the operator (and its CRDs) before bumping runtime images to a newer minor version.

## Recommended upgrade order

```
1. Pre-flight check ─► 2. Operator + CRDs ─► 3. Runtime images ─► 4. Validate ─► 5. Promote
```

### Step 1 \u2014 Pre-flight

- [ ] Read the release notes for the new version. Note any breaking changes, deprecated parameters, and CRD changes.
- [ ] Confirm the target version is compatible with the cluster's Kubernetes minor version.
- [ ] Apply the upgrade in a non-production cluster first.
- [ ] Take a backup of the operator chart values and the current `SpicepodSet` / `SpicepodCluster` manifests (Git is the source of truth; verify it is up to date).

### Step 2 \u2014 Upgrade the operator and CRDs

```bash
helm upgrade spiceai-operator \
  oci://709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/charts/spiceai-operator \
  --version <NEW_VERSION> \
  --values my-values.yaml
```

The chart applies updated CRDs automatically when `installCRDs: true`. For air-gapped or RBAC-restricted clusters, apply the CRDs separately:

```bash
spiceai-operator crd --apply
```

Watch the operator pod logs to confirm clean startup:

```bash
kubectl logs -n spiceai-operator-system -l spice.ai/app=spiceai-operator -f
```

### Step 3 \u2014 Upgrade runtime images

Bump the image tag (or, preferably, digest) on each `SpicepodSet` / `SpicepodCluster`. The operator's [`update_strategy`](../kubernetes/spicepodset.md) controls how the rollout proceeds.

```yaml
spec:
  spiceai_image_tag: <NEW_VERSION>-enterprise-models@sha256:abcd...
  update_strategy:
    type: RollingParallel
    max_unavailable: 1
```

For GitOps deployments, the bump is a single commit applied via Argo CD or Flux.

### Step 4 \u2014 Validate

Watch the rollout status:

```bash
kubectl get spicepodset -n spiceai
kubectl get pods -n spiceai -l spice.ai/app=<NAME>
```

Confirm the new replicas are healthy:

```bash
kubectl exec -it <pod> -- /spiced --version
curl -sf http://<service>:8090/health
curl -sf http://<service>:8090/v1/ready
```

Re-run the smoke-test query suite. Verify the [Grafana dashboard](observability.md#grafana-dashboard) shows steady query latency and no spike in `spiced_query_total{result="error"}`.

For `SpicepodCluster`, additionally verify that all executors have re-registered:

```bash
kubectl get spicepodcluster <NAME> -o jsonpath='{.status.executors.ready}'
```

### Step 5 \u2014 Promote

Once the non-production cluster has run for at least the bake time defined by the team's release policy (commonly 24\u201348 hours), promote the same digests to production.

## Rollback

The operator and the runtime are both rollback-safe. Roll back either layer independently.

### Roll back a runtime image

Re-apply the previous image tag (or digest):

```yaml
spec:
  spiceai_image_tag: <PREVIOUS_VERSION>-enterprise-models@sha256:1f4a...
```

The operator triggers a rolling rollback under the configured `update_strategy`.

For Helm-only deployments (no operator):

```bash
helm rollback spiceai <REVISION>
```

### Roll back the operator

```bash
helm rollback spiceai-operator <REVISION>
```

CRD rollbacks are **not** automatic. If the new CRDs added required fields, downgrade the CRDs explicitly:

```bash
spiceai-operator crd --apply  # uses the older operator binary
```

{% hint style="warning" %}
A CRD downgrade can fail if existing custom resources have populated fields that no longer exist in the older schema. Test CRD rollback in a non-production cluster as part of the upgrade dry run.
{% endhint %}

## Zero-downtime upgrade for distributed query

`SpicepodCluster` upgrades are zero-downtime when:

- At least two scheduler replicas are running.
- At least three executor replicas are running.
- `update_strategy.type` is `RollingOrdered` on the scheduler set.
- The object store has versioning enabled so a partial state write can be reverted.

The operator coordinates the rollout: schedulers upgrade first (one at a time), then executors. Active queries continue to run on the surviving nodes. Newly issued queries pin to the upgraded scheduler once it is `Ready`.

## Forced rollouts

Updating `annotations` or `labels` on a `SpicepodSet` triggers a full pod rollout, even if no other configuration has changed. Use this to force a refresh after rotating a Kubernetes Secret referenced via `env_from_source`:

```yaml
spec:
  annotations:
    secret-version: "v42"
```

## Operator deprecation policy

Deprecated CRD fields are marked with `# Deprecated:` in the operator's CRD schema and surfaced as warnings in `kubectl apply` output. Deprecations are removed no sooner than two minor versions after the deprecation announcement, giving operators a guaranteed migration window.
