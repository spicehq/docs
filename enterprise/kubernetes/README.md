---
description: Spice.ai Kubernetes Operator for automated deployment and lifecycle management.
icon: ship
---

# Kubernetes Operator

The Spice.ai Kubernetes Operator automates the deployment, scaling, and lifecycle management of Spice.ai workloads on Kubernetes. It provides two Custom Resource Definitions (CRDs), unified under the `spice.ai/v2` API version:

- **`SpicepodSet`** (`spice.ai/v2`) — Deploys and manages Spicepod replicas as one or more suffixed `StatefulSet`s.
- **`SpicepodCluster`** (`spice.ai/v2`) — Deploys a distributed query cluster with scheduler and executor nodes, secured with auto-provisioned mTLS certificates.

{% hint style="info" %}
`v2` consolidates the previous `spice.ai/v1` (`SpicepodSet`) and `spice.ai/v1alpha1` (`SpicepodCluster`) schemas. Existing `v1` / `v1alpha1` manifests keep working and are converted automatically — no immediate action required.
{% endhint %}

For a step-by-step walkthrough, see the [User Guide](user-guide.md). For exhaustive field references, see [SpicepodSet](spicepodset.md) and [SpicepodCluster](spicepodcluster.md).

## Installation

### Prerequisites

- Kubernetes 1.33+
- Helm 3.x

The Spice Kubernetes Operator is distributed through the [AWS Marketplace](../deployment/aws-marketplace.md) Spice.ai Enterprise listing. Subscribe and authenticate to the Marketplace ECR registry, then install. The chart renders and keeps the CRDs by default (`crds.enabled: true`, `crds.keep: true`).

### Helm

```bash
helm install spiceai-operator \
  oci://709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/spiceai-enterprise-plan:1.0.0-operator-helm \
  --namespace spiceai-operator-system --create-namespace
```

### Docker

```bash
docker pull 709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/spiceai-enterprise-plan:1.0.0-operator
```

{% hint style="info" %}
Multi-architecture (`linux/amd64` and `linux/arm64`) operator images are also published to GitHub Container Registry at `ghcr.io/spicehq/spiceai-operator`. Override the operator image with `--set image.repository=ghcr.io/spicehq/spiceai-operator`; the tag tracks the chart `appVersion` unless pinned with `--set image.tag=<version>`.
{% endhint %}

### Helm Values

| Parameter                                                  | Description                                                                     | Default                       |
| ---------------------------------------------------------- | ------------------------------------------------------------------------------- | ----------------------------- |
| `image.repository`                                         | Operator image path (registry + name)                                           | `…/spice-ai/spiceai-enterprise-plan` |
| `image.tag`                                                | Operator image tag (operator-suffixed chart `appVersion`)                       | `1.0.0-operator`              |
| `image.pullPolicy`                                         | Operator image pull policy                                                      | `IfNotPresent`                |
| `image.pullSecrets`                                        | Image pull secrets                                                              | —                             |
| `crds.enabled`                                             | Render the bundled CRDs with the chart                                          | `true`                        |
| `crds.keep`                                                | Annotate CRDs with `helm.sh/resource-policy: keep` so `helm uninstall` leaves them (and their custom resources) in place | `true`     |
| `installCRDs`                                              | **Deprecated** — prefer `crds.enabled` / `crds.keep`. A boolean here overrides both (`true` ≡ `{enabled: true, keep: true}`, `false` disables CRD rendering). Leave unset to use the `crds` block. | unset |
| `serviceAccount.create` / `.name` / `.annotations`         | Operator ServiceAccount (annotations for IRSA)                                  | `true` / chart name / `{}`    |
| `resources`                                                | CPU/memory `requests` and `limits` for the operator                             | —                             |
| `nodeSelector` / `tolerations` / `affinity`                | Operator pod scheduling                                                         | —                             |
| `serviceMonitor.enabled` / `.interval`                     | Prometheus `ServiceMonitor` for the operator                                    | `false` / `15s`               |
| `clusterDomain`                                            | Kubernetes cluster domain for internal DNS                                      | `cluster.local`               |
| `pauseCrashloopingPodsThreshold`                           | Crashloop threshold before pausing a `SpicepodSet` (`0` disables)               | `0`                           |
| `admissionPolicy`                                          | Admission validation strictness: `err` \| `warn` \| `off`                       | `err`                         |
| `sidecarInjector.enabled`                                  | Enable annotation-based sidecar injection                                       | `true`                        |
| `sidecarInjector.defaultImage` / `.defaultImagePullPolicy` | Defaults for injected sidecars                                                  | (operator default)            |
| `namespaces` / `denyNamespaces`                            | Scope the operator to / away from specific namespaces (mutually exclusive)      | (all namespaces)              |
| `telemetry.otlp.*`                                         | Push operator metrics to an OTLP collector (see [Operator Metrics](metrics.md)) | disabled                      |
| `telemetryProperties`                                      | Key/value pairs forwarded to the Spice runtime as telemetry properties          | `{}`                          |

## Managed Resources

For each `SpicepodSet`, the operator creates and manages:

1. **StatefulSet(s)** — One per replica (each with an ordinal suffix), managing the pods.
2. **ConfigMap** — Stores the `spicepod` YAML, mounted into the pod.
3. **Service** — `ClusterIP` service exposing fixed ports HTTP (`8080`), Flight (`50051`), and metrics (`9090`), mapped via `targetPort` to the configured Spiced ports. Disable with `service.enabled: false`.
4. **NetworkPolicy** — Only when `network.ingress` / `network.egress` is supplied; rules are written verbatim.
5. **ServiceAccount, Role, RoleBinding** — Only when `serviceAccount.enabled` and `serviceAccount.create` are both `true`.

## Workload Deployment

Every `SpicepodSet` is deployed as one or more `StatefulSet`s — one per replica, each with an ordinal suffix — so even single-replica workloads get a stable identity, ordered startup, and predictable DNS, with zero-downtime rollouts. Rolling updates, `BlueGreen` cutovers, and standby retention are expressed as parallel suffixed `StatefulSet`s. Pre-existing v0.x `Deployment`-based workloads are reliably grandfathered and continue to reconcile.

## Features

| Feature                                                              | SpicepodSet | SpicepodCluster |
| -------------------------------------------------------------------- | :---------: | :-------------: |
| Workload deployment (suffixed StatefulSets)                          |      ✓      |        ✓        |
| Update strategies (`RollingOrdered`, `RollingParallel`, `BlueGreen`) |      ✓      |        ✓        |
| Standby versions & instant rollback (SHA)                            |      ✓      |        —        |
| Persistent volume with auto-resize                                   |      ✓      |        ✓        |
| Zero-replica pausing                                                 |      ✓      |        ✓        |
| Crashloop protection                                                 |      ✓      |        ✓        |
| Forced rollouts via annotations/labels                               |      ✓      |        ✓        |
| Network policy management (opt-in)                                   |      ✓      |        ✓        |
| Service account configuration (incl. IRSA)                           |      ✓      |        ✓        |
| Health probe customization                                           |      ✓      |        ✓        |
| Pod scheduling (affinity, tolerations)                               |      ✓      |        ✓        |
| Admission validation                                                 |      ✓      |        ✓        |
| Status conditions (`Ready`, `Paused`)                                |      ✓      |        ✓        |
| Automatic mTLS certificates                                          |      —      |        ✓        |
| Distributed scheduler/executor topology                              |      —      |        ✓        |
| Prometheus metrics, ServiceMonitor & OTLP                            |      ✓      |        ✓        |

## Sidecar Injection

The operator can inject a Spice sidecar into any standard Kubernetes Pod by annotating the Pod template with `spice.ai/inject: "true"` and pointing `spice.ai/inject-config` at a `ConfigMap` in the same namespace that holds your `spicepod.yaml`:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: demo-spice-config
data:
  spicepod.yaml: |
    name: demo-sidecar
    kind: Spicepod
    version: v1
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-with-spice
spec:
  replicas: 1
  selector:
    matchLabels: { app: app-with-spice }
  template:
    metadata:
      labels: { app: app-with-spice }
      annotations:
        spice.ai/inject: "true"
        spice.ai/inject-config: demo-spice-config
    spec:
      containers:
        - name: app
          image: nginx:stable
```

### Supported annotations

| Annotation                   | Default         | Description                                                     |
| ---------------------------- | --------------- | --------------------------------------------------------------- |
| `spice.ai/inject`            | —               | `"true"` / `"false"` — enable or disable injection for the Pod. |
| `spice.ai/inject-config`     | —               | ConfigMap holding the spicepod.                                 |
| `spice.ai/inject-config-key` | `spicepod.yaml` | Key inside the ConfigMap data.                                  |
| `spice.ai/image`             | install default | Override the injected Spice image.                              |
| `spice.ai/image-pull-policy` | install default | Override the injected image pull policy.                        |
| `spice.ai/http-port`         | `18090`         | Sidecar HTTP port.                                              |
| `spice.ai/flight-port`       | `15051`         | Sidecar Arrow Flight port.                                      |
| `spice.ai/metrics-port`      | `19090`         | Sidecar Prometheus metrics port.                                |

{% hint style="info" %}
Injection runs on Pod creation, so place these annotations on the controller's Pod template (e.g. `Deployment.spec.template.metadata.annotations`) and `kubectl rollout restart` to pick up changes. The ConfigMap must already exist when the Pod is created. The webhook rejects Pods whose existing container ports collide with the requested sidecar ports.
{% endhint %}

Cluster operators can set defaults globally via Helm values `sidecarInjector.defaultImage` and `sidecarInjector.defaultImagePullPolicy`, with per-workload annotations as overrides. The sidecar injector is enabled by default (`sidecarInjector.enabled`); disable it with `--no-sidecar-injector`.

## Operator CLI

The released operator image ships only the `run` subcommand. The `crd` and `json-schema` subcommands below are development/tooling helpers compiled into debug builds only — they are not present in the standard release binary, and CRDs are installed via the Helm chart rather than `crd --apply`.

### `crd` — Output or apply CRD definitions

```bash
spiceai-operator crd              # Print CRD YAML to stdout
spiceai-operator crd --apply      # Apply CRDs to the current cluster
spiceai-operator crd --output FILE
```

### `run` — Start the operator controller

| Flag                                  | Default                   | Description                                                  |
| ------------------------------------- | ------------------------- | ------------------------------------------------------------ |
| `--health-probe-bind-address`         | `0.0.0.0:8090`            | Operator HTTP API / health probe bind address                |
| `--metrics-bind-address`              | `0.0.0.0:9090`            | Prometheus metrics bind address                              |
| `--webhook-bind-address`              | `0.0.0.0:8443`            | Admission / conversion webhook bind address                  |
| `--operator-namespace`                | `spiceai-operator-system` | Namespace for the operator (used for cluster-shared secrets) |
| `--cluster-domain`                    | `cluster.local`           | Kubernetes cluster domain                                    |
| `--admission-policy`                  | `err`                     | Admission validation strictness (`err` \| `warn` \| `off`)   |
| `--watch-namespaces`                  | (all)                     | Comma-separated namespaces to watch exclusively              |
| `--deny-namespaces`                   | —                         | Comma-separated namespaces to exclude from watching          |
| `--enable-sidecar-injector`           | `true`                    | Enable sidecar injection (`--no-sidecar-injector` disables)  |
| `--pause-crashlooping-pods-threshold` | `10`                      | Dead pod observations before pausing (`0` disables)          |
| `--telemetry-properties KEY=VALUE`    | —                         | Key/value pairs forwarded to the Spice runtime               |
| `--verbose`                           | `false`                   | Enable debug-level logging                                   |

### `json-schema` — Output the OpenAPI v3 JSON schema for the `SpicepodSet` CRD

```bash
spiceai-operator json-schema              # Print to stdout
spiceai-operator json-schema --output FILE
```

## Operator HTTP API

The operator serves an HTTP API on `--health-probe-bind-address` (`0.0.0.0:8090` by default). The standard build exposes only the health and readiness endpoints:

| Endpoint  | Method | Description                                            |
| --------- | ------ | ------------------------------------------------------ |
| `/health` | GET    | Health check — returns `OK`                            |
| `/ready`  | GET    | Readiness probe — `200` once the operator has bootstrapped, `503` otherwise |

### Pod-status API (deprecated)

{% hint style="warning" %}
The pod-status endpoints below are **deprecated** and slated for removal in a future release. They are **not** compiled into the standard operator build - they are available only in builds with the `status-api` feature enabled, which logs a deprecation warning on startup. Do not rely on them for new integrations.
{% endhint %}

| Endpoint                                   | Method | Description                        |
| ------------------------------------------ | ------ | ---------------------------------- |
| `/{namespace}/{name}`                      | GET    | Pod status for a `SpicepodSet`     |
| `/{namespace}/{name}?kind=SpicepodCluster` | GET    | Pod status for a `SpicepodCluster` |

The pod-status response includes per-pod details (name, UID, phase, IP, port, start time, Spiced health/readiness, and any error reason/message). For paused `SpicepodSet`s (`replicas: 0`), the response includes `paused: true` with a `pauseReason`.

## Upgrading

```bash
helm upgrade spiceai-operator \
  oci://709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/charts/spiceai-operator \
  --values my-values.yaml
```

`v2` is served with automatic conversion of legacy `v1` / `v1alpha1` resources, so existing manifests continue to apply after the upgrade. See the [User Guide](user-guide.md) for rollout guidance and the per-CRD field changes in [SpicepodSet](spicepodset.md#migrating-from-spiceaiv1) and [SpicepodCluster](spicepodcluster.md).

## Roadmap

Capabilities in active development and planned for the operator include:

- **Leader election** for multi-replica operator high availability.
- **Custom API-server TLS** with a user-supplied certificate and CA bundle.
- **Log drains** to forward Spicepod logs to external sinks (Datadog, Splunk, CloudWatch, …).
- **Audit logging** of operator and workload lifecycle events.
- **Cedar policy enforcement** distributed to Spicepod pods.
- **Auto-scaling** via `HorizontalPodAutoscaler` and Spiced-specific metrics.
- **Backup & restore** of stateful volumes via `VolumeSnapshot`.
- **Secret rotation** with automatic rolling restarts.
- **Spice Cloud Platform integration** for centralized fleet management and observability.
