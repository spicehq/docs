---
description: SpicepodSet CRD reference for deploying and managing Spicepod replicas.
icon: layer-group
---

# SpicepodSet

A `SpicepodSet` (`spice.ai/v2`) deploys and manages one or more Spicepod replicas. The operator handles the full lifecycle: creating the workload, rolling updates, volume management, health monitoring, and crashloop protection.

Every `SpicepodSet` is deployed as one or more `StatefulSet`s — one per replica, each with an ordinal suffix — giving every pod a stable identity, ordered startup, and a predictable network hostname, even for single-replica workloads. Rollouts, `BlueGreen` cutovers, and standby retention are all expressed as parallel, suffixed `StatefulSet`s.

{% hint style="info" %}
`v2` is the current schema. Legacy `spice.ai/v1` `SpicepodSet` manifests continue to apply unchanged and are losslessly converted by the operator, and pre-existing v0.x `Deployment`-based workloads are grandfathered. See [Migrating from `spice.ai/v1`](#migrating-from-spiceaiv1) for the field-by-field mapping.
{% endhint %}

## Minimal Example

```yaml
apiVersion: spice.ai/v2
kind: SpicepodSet
metadata:
  name: my-spicepod
  namespace: default
spec:
  replicas: 1
  spicepod:
    name: my-spicepod
    kind: Spicepod
    version: v1
```

The `spicepod` field is a structured object holding the inline Spicepod definition (datasets, catalogs, models, views, etc.) and is preserved as-is by the operator. It is stored in a `ConfigMap` and mounted into each pod; changing it triggers a rollout.

## Container Image

```yaml
spec:
  image:
    repository: 709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/spiceai-enterprise-plan
    tag: 2.0.0-enterprise-models
    pullPolicy: IfNotPresent          # Always | Never | IfNotPresent
    pullSecret: my-pull-secret
```

| Field        | Default                                     | Description                                                             |
| ------------ | ------------------------------------------- | ----------------------------------------------------------------------- |
| `repository` | `…/spice-ai/spiceai-enterprise-plan`        | Full image path (registry + name).                                      |
| `tag`        | `2.0.0-enterprise-models`                   | Image tag.                                                              |
| `pullPolicy` | `Always` for `:latest`, else `IfNotPresent` | Image pull policy (`Always` \| `Never` \| `IfNotPresent`).              |
| `pullSecret` | —                                           | Name of a Kubernetes Secret holding credentials for a private registry. |

`repository` is the full registry + name path. The enterprise default is pulled from the AWS Marketplace ECR registry — provide credentials via `pullSecret`. To use a public build instead, set e.g. `repository: spiceai/spiceai` (Docker Hub) or `repository: ghcr.io/spiceai/spiceai`.

## Ports

```yaml
spec:
  http:
    port: 8090
  flight:
    port: 50051
  metrics:
    port: 9090
```

| Field          | Default | Description               |
| -------------- | ------- | ------------------------- |
| `http.port`    | `8090`  | HTTP API port.            |
| `flight.port`  | `50051` | Apache Arrow Flight port. |
| `metrics.port` | `9090`  | Prometheus metrics port.  |

These configure what the Spiced container listens on. The Service exposes fixed ports `8080` (HTTP), `50051` (Flight), and `9090` (metrics) and maps `targetPort` to the configured Spiced ports above. Health probes target `http.port`.

## Resources

```yaml
spec:
  resources:
    requests:
      cpu: 200m
      memory: 1Gi
    limits:
      cpu: "2"
      memory: 4Gi
```

## Environment Variables and Secrets

```yaml
spec:
  env:
    - name: SPICE_LOG_LEVEL
      value: debug
    - name: MY_SECRET
      valueFrom:
        secretKeyRef:
          name: my-secret
          key: api-key
  envFromSource:
    - configMapRef:
        name: my-config
    - secretRef:
        name: my-secret
```

The Spicepod YAML supports `${secrets:KEY}` references to Kubernetes Secret values. Inject the backing Secret with `envFromSource` so the value is resolvable inside the pod at runtime.

## Persistent Storage

Add persistent volumes to each pod replica with `volumeClaimTemplates`, a list whose entries mirror the upstream `PersistentVolumeClaimTemplate` shape. The operator creates a `PersistentVolumeClaim` per entry as part of each `StatefulSet`. The template named `data` (the name defaults to `data` when `metadata.name` is omitted) is auto-mounted at `/data` with no `volumeMounts` entry required; any other template must be paired with an explicit `volumeMounts` entry of the same name:

```yaml
spec:
  volumeClaimTemplates:
    - metadata:
        name: data
      spec:
        storageClassName: standard
        resources:
          requests:
            storage: 10Gi
```

The full PVC spec is available, including `accessModes`, `volumeMode`, and `selector`. Increasing a template's `spec.resources.requests.storage` triggers automatic PVC resizing, provided the `StorageClass` has `allowVolumeExpansion: true`.

{% hint style="warning" %}
Volume shrinking is not supported. Decreasing the storage request has no effect on existing PVCs. Under the `BlueGreen` strategy, PVCs behave like ephemeral storage — each generation gets new PVCs, so volume data is not preserved across cutovers.
{% endhint %}

## Service Account

```yaml
# Operator-managed
spec:
  serviceAccount:
    enabled: true
    create: true

# Bring your own
spec:
  serviceAccount:
    enabled: true
    create: false
    name: my-existing-service-account

# With IRSA annotations
spec:
  serviceAccount:
    enabled: true
    create: true
    annotations:
      eks.amazonaws.com/role-arn: arn:aws:iam::123456789012:role/spice-ai-role
```

| Field         | Default            | Description                                                                                       |
| ------------- | ------------------ | ------------------------------------------------------------------------------------------------- |
| `enabled`     | `false`            | Whether to use a ServiceAccount.                                                                  |
| `create`      | `false`            | Whether to create a new ServiceAccount. If `false`, set `name` to reference an existing one.      |
| `name`        | `SpicepodSet` name | ServiceAccount name; required when `create: false`.                                               |
| `annotations` | —                  | Annotations applied **only** to the ServiceAccount (ideal for IRSA `eks.amazonaws.com/role-arn`). |

## Update Strategies

```yaml
spec:
  updateStrategy:
    type: RollingOrdered    # default
```

| Strategy                       | Behavior                                                                                                                                                                                                                          |
| ------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`RollingOrdered`** (default) | Updates pods one at a time in ordinal order, waiting for each to become Ready before proceeding.                                                                                                                                  |
| **`RollingParallel`**          | Updates pods in parallel, keeping at least one available. Set `maxUnavailable` to bound concurrent unavailable pods.                                                                                                              |
| **`BlueGreen`**                | Brings up a complete parallel set of `StatefulSet`s for the new generation, then atomically switches the Service to it once the new generation is Ready and tears down the old generation. PVCs are ephemeral across generations. |

```yaml
spec:
  replicas: 5
  updateStrategy:
    type: RollingParallel
    maxUnavailable: 2
    minReadyForCutover: 3   # optional
```

{% hint style="info" %}
The legacy `Parallel` strategy has been **removed**. Existing resources that specify `Parallel` are converted to the default `RollingOrdered`. Use `RollingParallel` with `maxUnavailable` for concurrent updates.
{% endhint %}

### `minReadyForCutover`

Switches the Service to the new generation as soon as `minReadyForCutover` new-generation pods are Ready, instead of waiting for all of them. This is useful when the load profile is satisfied by fewer than the full replica count and a faster switchover is preferable to running at peak capacity for the whole rollout.

- **For `BlueGreen` and instant rollback**: switches the version-pinned Service selector and promotes `status.activeVersion` early.
- **For `RollingOrdered` / `RollingParallel`**: causes the Service object to be created earlier in the rollout (selector is not version-pinned, so traffic is already routed to all matching Ready pods once the Service exists).
- A value of `0` is treated as unset. Values larger than `replicas` collapse to "wait for all".

## Standby Versions and Instant Rollback

Retain the previous-generation pods after a rollout so traffic can be cut back to them instantly, without waiting for pods to reschedule.

```yaml
spec:
  updateStrategy:
    type: BlueGreen
  standbyVersion:
    enabled: true
    retentionPeriodSeconds: 900   # default 15 minutes
```

When `standbyVersion.enabled: true`, the previous version's workload is retained for `retentionPeriodSeconds` after a successful rollout. The operator tracks a content-based SHA of the deployment-affecting spec fields in the `spice.ai/version` label and exposes it as `status.activeVersion` / `status.standbyVersion`.

**To roll back, re-apply the previous `spec`** (e.g. `helm rollback`, `kubectl apply` of an earlier manifest, or a Git revert). The operator detects that the incoming version SHA matches the retained standby, then swaps the active and standby workloads in place — patching the version-pinned Service selector immediately, without restarting pods. Non-deployment changes (such as editing `retentionPeriodSeconds` or the update strategy) do not create a new version or undo a rollback.

{% hint style="info" %}
Because the trigger is the spec SHA itself, instant rollback is GitOps-compatible (ArgoCD / Flux): reverting the manifest in Git is sufficient, with no operator-owned annotation to round-trip and no `ignoreDifferences` configuration required. Combine `standbyVersion` with `BlueGreen` for the canonical zero-downtime production rollout pattern.
{% endhint %}

## Scaling and Pausing

Set `replicas: 0` to pause the workload while retaining supporting resources (Service, ConfigMap, ServiceAccount, and any user-supplied NetworkPolicy):

```yaml
spec:
  replicas: 0
```

## Network and DNS

A `NetworkPolicy` is **opt-in**: the operator only creates one when you supply `network.ingress` and/or `network.egress`, and it writes those rules **verbatim** — no implicit operator-namespace ingress, DNS egress, or cluster-peer rules are appended. If neither field is set, no `NetworkPolicy` is created and pod traffic follows the cluster default.

```yaml
spec:
  network:
    ingress:
      - from:
          - namespaceSelector:
              matchLabels:
                name: my-namespace
        ports:
          - protocol: TCP
            port: 8080
    egress:
      # Application traffic.
      - to:
          - ipBlock:
              cidr: 10.0.0.0/8
        ports:
          - protocol: TCP
            port: 443
      # DNS — required in most clusters; the operator no longer adds this for you.
      - to:
          - namespaceSelector:
              matchLabels:
                kubernetes.io/metadata.name: kube-system
        ports:
          - protocol: UDP
            port: 53
          - protocol: TCP
            port: 53
    dnsPolicy: None
    dnsConfig:
      nameservers:
        - 8.8.8.8
      searches:
        - my-domain.local
```

The validating admission webhook **warns** when `network.egress` is set without a DNS (UDP/TCP 53) rule, and **rejects** cluster-mode workloads whose policy omits a peer rule selecting `spice.ai/cluster=<name>`. Adjust strictness with the `spice.ai/validation-level` annotation (`err` / `warn` / `off`).

Disable the Service entirely:

```yaml
spec:
  service:
    enabled: false
```

## Annotations and Labels

`annotations` and `labels` are propagated to **all** resources the operator creates (StatefulSets, Service, ConfigMap, etc.). Updating either triggers a full pod rollout, even when no other configuration has changed — a convenient way to force a restart:

```yaml
spec:
  annotations:
    custom-annotation: test-value
  labels:
    environment: production
    team: data-platform
```

Labels and annotations are `kebab-case`, and the operator's own keys are namespaced under `spice.ai/`. Reserved labels are `spice.ai/app`, `spice.ai/spicepod`, `spice.ai/version`, `spice.ai/cluster`, `spice.ai/cluster-role`, `spice.ai/cluster-mtls`, and `spice.ai/component`; reserved annotations are `spice.ai/sidecar-injected`, `spice.ai/observed-generation`, and `spice.ai/validation-level`. The admission controller **rejects** attempts to set these reserved keys on `spec.annotations` or `spec.labels`.

{% hint style="info" %}
To annotate only the ServiceAccount (e.g. for IRSA), use `serviceAccount.annotations` — those are not propagated to other resources.
{% endhint %}

## Health Probes

Probes are only created when the Spiced HTTP server is enabled (i.e. for non-executor nodes). Cluster executors have no probes.

```yaml
spec:
  probes:
    liveness:
      initialDelaySeconds: 10
      timeoutSeconds: 5
      periodSeconds: 30
      failureThreshold: 5
    readiness:
      initialDelaySeconds: 5
      periodSeconds: 10
```

## Pod Scheduling

```yaml
spec:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
        - matchExpressions:
            - key: kubernetes.io/arch
              operator: In
              values: [amd64]
  tolerations:
    - key: dedicated
      operator: Equal
      value: spice
      effect: NoSchedule
  terminationGracePeriodSeconds: 30
```

## Crashloop Protection

The operator monitors pods for repeated failures, combining two signals: accumulated dead pods (Failed + Succeeded) and pod failure events (`BackOff`, `Evicted`, `OOMKilling`, `Failed`). When a `SpicepodSet` exceeds the configured threshold, the operator pauses the workload (`replicas → 0`), sets `status.pauseReason = CrashLooping`, emits a Warning event, and surfaces the state through `status.conditions`. To resume, fix the configuration and set `replicas` back to the desired count.

Configure the threshold via the operator CLI flag `--pause-crashlooping-pods-threshold` (Helm value `pauseCrashloopingPodsThreshold`). The binary defaults to `10`, but the chart ships `0` (crashloop protection **disabled**) by default — set a positive integer to enable it.

## Admission Validation

A validating admission webhook checks each `SpicepodSet` at apply time rather than at reconcile time. Validations include:

- **Spicepod schema** — the `spicepod` payload is parsed against the schema selected by its inline `version:` field, with errors identifying the offending fields.
- **NetworkPolicy invariants** — warns when `network.egress` omits a DNS rule; rejects cluster-mode resources missing an intra-cluster peer rule.
- **Reserved labels/annotations** — rejects operator-reserved keys on `spec.labels` / `spec.annotations`, and enforces label length limits.
- **Volume storage class** — validates that the requested `storageClassName` exists.

The default strictness is set operator-wide with `--admission-policy` (Helm `admissionPolicy`): `err` (reject, default), `warn` (allow with a warning), or `off`. Override per-resource with the `spice.ai/validation-level` annotation (`err` / `warn` / `off`).

## Status

Useful fields on `status`:

| Field                             | Description                                                       |
| --------------------------------- | ----------------------------------------------------------------- |
| `replicas`                        | Formatted replica state for `kubectl` display, e.g. `2/5`.        |
| `readyReplicas` / `totalReplicas` | Numeric replica counts.                                           |
| `role`                            | `<none>`, `scheduler`, or `executor`.                             |
| `pauseReason`                     | Set when paused (e.g. `CrashLooping`).                            |
| `activeVersion`                   | Spec SHA of the version currently receiving traffic.              |
| `standbyVersion`                  | Spec SHA of the standby retained for instant rollback.            |
| `standbyExpiresAt`                | RFC 3339 timestamp when the standby pods will be reclaimed.       |
| `conditions`                      | Standard Kubernetes `Condition`s, including `Ready` and `Paused`. |

The `conditions` array makes a `SpicepodSet` compatible with native Kubernetes tooling:

```bash
kubectl wait spicepodset/my-spicepod --for=condition=Ready
kubectl wait spicepodset/my-spicepod --for=condition=Paused=False

kubectl get spicepodset
# NAME          READY   ROLE     AGE   LAST UPDATED
# my-spicepod   3/3     <none>   2d    1m
```

## Monitoring

Telemetry properties passed to the Spice runtime are configured at the operator level (Helm values), not on the `SpicepodSet`:

```yaml
# Helm values for the operator
telemetryProperties:
  environment: production
  team: data-platform
  region: us-west-2
```

For operator self-telemetry (controller reconcile counts/durations, Kubernetes API latencies, certificate expiry) over Prometheus and OTLP, see [Operator Metrics](metrics.md).

## Migrating from `spice.ai/v1`

`v2` renames several fields to follow standard Kubernetes API conventions. Existing `spice.ai/v1` resources keep working and are converted automatically, but manifests authored natively against `v2` should adopt the new shape.

| `spice.ai/v1`                                        | `spice.ai/v2`                                                             |
| ---------------------------------------------------- | ------------------------------------------------------------------------- |
| `spicepod: \|` (string-encoded YAML)                 | `spicepod:` (structured object)                                           |
| `spiceai_image_registry` / `spiceai_image_name`      | `image.repository` (full path)                                            |
| `spiceai_image_tag`                                  | `image.tag`                                                               |
| `httpPort` / `flightPort` / `metricsPort`            | `http.port` / `flight.port` / `metrics.port`                              |
| `enableService`                                      | `service.enabled`                                                         |
| `volume.storageClassName` / `volume.storageRequests` | `volumeClaimTemplates[].spec.{storageClassName, resources.requests.storage}` |
| `update_strategy`                                    | `updateStrategy` (camelCase; `Parallel` removed)                          |
| `instantRollback`                                    | `standbyVersion` (SHA-based; re-apply previous spec)                      |

All status timestamps are now RFC 3339 strings, and labels/annotations are `kebab-case` with the legacy `app` label namespaced as `spice.ai/app`. For the full field-by-field guide, see the [operator upgrade guide](https://github.com/spicehq/spice-k8s-operator).
