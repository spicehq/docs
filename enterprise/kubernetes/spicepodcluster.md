---
description: SpicepodCluster CRD reference for deploying distributed query clusters.
icon: circle-nodes
---

# SpicepodCluster

A `SpicepodCluster` (`spice.ai/v2`) deploys a distributed query cluster with dedicated scheduler and executor nodes. The operator automatically manages mTLS certificate provisioning, child `SpicepodSet` resources, and cluster topology.

{% hint style="info" %}
`v2` is the current schema. Legacy `spice.ai/v1alpha1` `SpicepodCluster` manifests continue to apply unchanged and are converted automatically. The main renames are `schedulerSetSpec` / `executorSetSpec` → `schedulerSpec` / `executorSpec`, plus the `status` certificate fields; see [Status](#status).
{% endhint %}

## Architecture

```text
             ┌─────────────────────┐
             │    Load Balancer    │
             └─────────────────────┘
                        │
     ┌──────────────────┼──────────────────┐
     ▼                  ▼                  ▼
┌──────────┐     ┌──────────┐     ┌──────────┐
│Scheduler │     │Scheduler │     │Scheduler │◄──► Object Store (S3)
└──────────┘     └──────────┘     └──────────┘
     ▲                  ▲                  ▲
     │    (executor-initiated connections) │
     │                  │                  │
┌──────────┐     ┌──────────┐     ┌──────────┐
│ Executor │     │ Executor │     │ Executor │────► Object Store (shuffle)
└──────────┘     └──────────┘     └──────────┘
```

Schedulers coordinate query planning and partition assignment; executors perform the compute work. Executors initiate connections to all schedulers and are shared across them — partition ownership is committed to a shared object store and pushed to executors via the cluster `ControlStream`. See [Distributed Query](../features/distributed-query.md) for the full execution model.

## Example

```yaml
apiVersion: spice.ai/v2
kind: SpicepodCluster
metadata:
  name: my-cluster
  namespace: default
spec:
  schedulerSpec:
    replicas: 1
    spicepod:
      version: v1
      kind: Spicepod
      name: my-cluster-scheduler
    resources:
      requests:
        cpu: 100m
        memory: 256Mi
      limits:
        cpu: 500m
        memory: 512Mi
  executorSpec:
    replicas: 3
    resources:
      requests:
        cpu: 200m
        memory: 512Mi
      limits:
        cpu: "1"
        memory: 2Gi
```

Executors pull their Spicepod configuration from the scheduler, so no `spicepod` field is needed on `executorSpec`.

## Multi-Replica Schedulers

For high availability, deploy multiple schedulers:

```yaml
spec:
  schedulerSpec:
    replicas: 2
```

Each scheduler independently accepts and distributes work. Shared state is maintained in an S3-compatible object store.

## Automatic mTLS

The operator automatically:

1. Generates a self-signed root CA certificate.
2. Issues per-node leaf certificates with appropriate SANs.
3. Configures mTLS between all cluster nodes.
4. Stores certificates in Kubernetes Secrets.
5. Tracks certificate expiry via Prometheus metrics.

### Development Mode

For development and testing, mTLS can be disabled:

```yaml
spec:
  allowInsecureConnections: true
```

{% hint style="danger" %}
Never use `allowInsecureConnections: true` in production. All inter-node communication will be unencrypted.
{% endhint %}

## Port Separation

| Port  | Visibility   | Services                        | mTLS Required |
| ----- | ------------ | ------------------------------- | ------------- |
| 50051 | Public       | Arrow Flight, OpenTelemetry     | Optional      |
| 8090  | Public       | HTTP API                        | Optional      |
| 9090  | Public       | Prometheus metrics              | No            |
| 50052 | **Internal** | Scheduler gRPC, Cluster Service | **Required**  |

The internal port (50052) carries cluster coordination traffic. See [Distributed Query → Internal gRPC](../features/distributed-query.md#internal-grpc-port-50052) for the full RPC surface, including:

- **`GetAppDefinition`** — Executors fetch the full Spicepod definition (datasets, catalogs, views, UDFs) from the scheduler.
- **`ExpandSecret`** — Executors request secret values from the scheduler's secret store.
- **`GetSchedulers`** / **`AllocateInitialPartitions`** — Executors fetch scheduler membership and their assigned partitions at startup.
- **`ControlStream`** — Bidirectional channel carrying executor heartbeats and `UpdatePartitions` / `RefreshDataset` / `CancelTasks` commands.
- **`GetTaskHistory`** / **`GetMetrics`** — Federated `runtime.task_history` and on-demand metrics fan-out across the cluster.

## Verification

```bash
# Check cluster status
kubectl get spicepodcluster my-cluster

# List child SpicepodSets
kubectl get spicepodset

# Check pods by role
kubectl get pods -l spice.ai/cluster=my-cluster
kubectl get pods -l spice.ai/cluster-role=scheduler
kubectl get pods -l spice.ai/cluster-role=executor
```

## Configuration Inheritance

`SpicepodCluster` creates child `SpicepodSet` resources for schedulers and executors. Both `schedulerSpec` and `executorSpec` accept the common [`SpicepodSet` spec fields](spicepodset.md): `image`, `http`, `flight`, `metrics`, `replicas`, `resources`, `env`, `envFromSource`, `network`, `nodeAffinity`, `tolerations`, `volumeClaimTemplates`, `volumeMounts`, `serviceAccount`, `annotations`, `labels`, `updateStrategy`, `terminationGracePeriodSeconds`, and a per-node `cluster` override.

Notable differences from a standalone `SpicepodSet`:

- `executorSpec` does **not** accept `spicepod` or `probes` — executors fetch the Spicepod definition from the scheduler at startup via `GetAppDefinition` and run without the HTTP server that probes target.
- The `service` toggle is not available on cluster node specs; the operator manages the headless Services required for mTLS and scheduler/executor discovery.

### Per-node `cluster` overrides

The `cluster` field on `schedulerSpec` / `executorSpec` is a small subset (`NodeClusterConfig`) used to override cluster-internal addresses; the operator otherwise auto-populates cluster identity, role, mTLS, and scheduler discovery:

```yaml
spec:
  schedulerSpec:
    cluster:
      bindAddress: "0.0.0.0:50052"
```

## Status

```bash
kubectl get spicepodcluster my-cluster -o yaml
```

| Field                       | Description                                                                               |
| --------------------------- | ----------------------------------------------------------------------------------------- |
| `rootCertificateReady`      | Whether the cluster's root CA has been generated.                                         |
| `rootCertificateSecretName` | Secret holding the root CA certificate and private key.                                   |
| `rootCertificateExpiresAt`  | RFC 3339 expiration of the root CA.                                                       |
| `schedulerSpicepodsetName`  | Name of the child scheduler `SpicepodSet`.                                                |
| `executorSpicepodsetName`   | Name of the child executor `SpicepodSet`.                                                 |
| `schedulerReadyReplicas`    | Ready scheduler replicas.                                                                 |
| `executorReadyReplicas`     | Ready executor replicas.                                                                  |
| `conditions`                | Standard Kubernetes `Condition`s (`Ready`, `Paused`); supersede the legacy `error` field. |
