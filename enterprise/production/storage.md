---
description: Storage and acceleration tiering for production Spice.ai Enterprise deployments.
icon: hard-drive
---

# Storage

Spice.ai accelerations are **latency- and IOPS-sensitive**. Picking the right storage tier is the single highest-impact decision for query performance in a production deployment. This page is the reference for choosing storage for `SpicepodSet`, `SpicepodCluster`, and standalone runtime deployments.

## Storage tiers

Choose storage in this order of preference:

### 1. Local NVMe (recommended)

Node-local NVMe SSDs deliver the lowest latency and highest IOPS available on every major cloud and on-prem platform. Local NVMe is the default recommendation for accelerator volumes and for `executor` shuffle scratch in `SpicepodCluster`.

| Cloud      | Instance families with attached NVMe                                                                                                                                                                                                                                              |
| ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **AWS**    | `i4i`, `i7ie`, `i8g`, and any `d`-suffixed family (`m6id`, `m7gd`, `c7gd`, `r7gd`, `m6gd`, `c6gd`, `r6gd`).                                                                                                                                                                       |
| **Azure**  | [`Lsv3` / `Lasv3`](https://learn.microsoft.com/azure/virtual-machines/lsv3-series), [`Ddsv5` / `Ddsv6`](https://learn.microsoft.com/azure/virtual-machines/ddv5-ddsv5-series), [`Edsv5` / `Edsv6`](https://learn.microsoft.com/azure/virtual-machines/edv5-edsv5-series).         |
| **GCP**    | [Local SSD machine types](https://cloud.google.com/compute/docs/disks/local-ssd) \u2014 `*-lssd` variants of N2, C3, and Z3.                                                                                                                                                       |
| **On-prem**| Any node with locally-attached NVMe SSDs (Intel Optane, Samsung PM9A3, Micron 7450, etc.).                                                                                                                                                                                       |

Expose local NVMe to Kubernetes using the [Local Volume Static Provisioner](https://github.com/kubernetes-sigs/sig-storage-local-static-provisioner) as a `local-storage` `StorageClass`, then point the `SpicepodSet` `volume.storage_class_name` at it:

```yaml
apiVersion: spice.ai/v1
kind: SpicepodSet
metadata:
  name: prod-accelerator
spec:
  replicas: 1
  volume:
    storage_class_name: local-storage
    storage_requests: 500Gi
  spicepod: |
    name: prod-accelerator
    kind: Spicepod
    version: v1
```

{% hint style="warning" %}
Only deploy the Local Volume Static Provisioner together with its [stale-PV/PVC cleanup controller](https://github.com/kubernetes-sigs/sig-storage-local-static-provisioner/pull/385), which requires **provisioner `v2.6.0` or later** (released Aug 2023, [v2.6.0 release notes](https://github.com/kubernetes-sigs/sig-storage-local-static-provisioner/releases/tag/v2.6.0)). Without the cleanup controller, when a node is replaced (EC2 instance refresh, autoscaler scale-in, spot reclaim, etc.) the PVC remains bound to the deleted node and the pod will not reschedule until the PVC is manually deleted — a frequent production footgun. The cleanup controller runs as a separate `Deployment` alongside the per-node `DaemonSet`; see the [provisioner deployment docs](https://github.com/kubernetes-sigs/sig-storage-local-static-provisioner/tree/master/docs) for the required RBAC and configuration.
{% endhint %}

{% hint style="warning" %}
Local NVMe does not survive node replacement. Pair local volumes with a refresh strategy (refresh-on-startup datasets) or rely on the underlying source of truth for re-hydration. Always size accelerations smaller than the local NVMe so eviction never triggers.
{% endhint %}

### 2. High-IOPS network block storage

When persistence must survive node replacement \u2014 for example, large file-based accelerations or `SpicepodCluster` scheduler state \u2014 use a high-IOPS block storage class. Network block storage is slower than local NVMe but recoverable.

| Cloud      | First choice                                                                                                                  | Fallback                                                          |
| ---------- | ----------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------- |
| **AWS**    | [Amazon EBS `io2` Block Express](https://docs.aws.amazon.com/ebs/latest/userguide/ebs-volume-types.html#io2-bx) (sub-ms, 256K IOPS) | EBS `gp3` with provisioned IOPS                                   |
| **Azure**  | [Premium SSD v2](https://learn.microsoft.com/azure/virtual-machines/disks-types#premium-ssd-v2) (sub-ms, 80K IOPS)            | Premium SSD (`managed-csi-premium`)                               |
| **GCP**    | [Hyperdisk Extreme](https://cloud.google.com/compute/docs/disks/hyperdisks)                                                   | Persistent Disk SSD (`pd-ssd`)                                    |

Provision a custom `StorageClass` per cloud:

{% tabs %}
{% tab title="AWS (io2 Block Express)" %}
```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: spice-io2
provisioner: ebs.csi.aws.com
parameters:
  type: io2
  iopsPerGB: "500"
volumeBindingMode: WaitForFirstConsumer
allowVolumeExpansion: true
```
{% endtab %}

{% tab title="Azure (Premium SSD v2)" %}
```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: spice-pv2
provisioner: disk.csi.azure.com
parameters:
  skuName: PremiumV2_LRS
  DiskIOPSReadWrite: "20000"
  DiskMBpsReadWrite: "1000"
volumeBindingMode: WaitForFirstConsumer
allowVolumeExpansion: true
```
{% endtab %}

{% tab title="GCP (Hyperdisk Extreme)" %}
```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: spice-hyperdisk
provisioner: pd.csi.storage.gke.io
parameters:
  type: hyperdisk-extreme
  provisioned-iops-on-create: "20000"
volumeBindingMode: WaitForFirstConsumer
allowVolumeExpansion: true
```
{% endtab %}
{% endtabs %}

### 3. Cayenne shared object storage (Cayenne only)

For [Cayenne](https://spiceai.org/docs/components/data-accelerators/cayenne) acceleration that must be shared across replicas or persisted independently of pod lifecycle, point Cayenne at object storage:

- **AWS**: [S3 Express One Zone](https://aws.amazon.com/s3/storage-classes/express-one-zone/) directory buckets deliver single-digit-millisecond latency.
- **Azure**: ADLS Gen2 hot tier, or Premium block blob.
- **GCP**: Cloud Storage with the `Standard` storage class.

Object-store-backed Cayenne is the recommended pattern for `SpicepodCluster` deployments where multiple executors must share the same accelerated dataset.

### Not recommended for accelerations

Network file systems trade latency for sharing semantics. Do not use them as acceleration storage classes:

- **AWS EFS** \u2014 NFS-style latency. Acceptable for stateless artefacts but not accelerators.
- **Azure Files (`azurefile-csi`)** \u2014 SMB / NFS protocol overhead. Use only for `ReadWriteMany` shared artefacts.
- **GCP Filestore** \u2014 same trade-off as the above.

## Sizing accelerations

Spice acceleration storage is sized as: `dataset_size_at_max_lookback * compression_ratio * 1.3` to leave headroom for query temp files.

| Acceleration engine | Typical compression vs. raw Parquet | Notes                                                                              |
| ------------------- | ----------------------------------- | ---------------------------------------------------------------------------------- |
| **Cayenne**         | 1.0x \u2013 1.5x larger             | Uses Vortex columnar format. Best for analytical workloads and shared persistence. |
| **DuckDB**          | 0.7x \u2013 1.2x                    | Best for general-purpose analytical workloads.                                     |
| **SQLite / Postgres** | 1.5x \u2013 3x                    | Use only for OLTP-shaped workloads.                                                |
| **Arrow** (memory)  | 1.5x \u2013 2x                      | Memory-resident. Sized as memory, not disk.                                        |

Validate sizing under representative load using `runtime.task_history` and the [Grafana dashboard](observability.md#grafana-dashboard).

## Object store for `SpicepodCluster` shared state

`SpicepodCluster` requires an S3-compatible object store for shared scheduler state and shuffle data. Configure it once per cluster:

| Property                  | Recommended value                                                            |
| ------------------------- | ---------------------------------------------------------------------------- |
| **Bucket type**           | Single-purpose bucket per cluster.                                           |
| **Region**                | Same region as the executors. Cross-region object stores will dominate query latency. |
| **Versioning**            | Enabled. Required to recover from corrupted writes.                          |
| **Lifecycle**             | Expire `shuffle/*` after 7 days; expire `scheduler/state/*` after 30 days.   |
| **Encryption**            | Server-side encryption with KMS-managed keys.                                |
| **Access**                | Workload identity / IRSA on the cluster's `ServiceAccount`. No static keys.  |

## Disaster recovery

Production Spice.ai Enterprise deployments are recoverable from three sources of truth:

1. **Spicepod manifests in Git** \u2014 the canonical source of dataset, model, and acceleration configuration. Drives the Argo CD / Flux pipeline.
2. **Object-store-backed cluster state** \u2014 for `SpicepodCluster`, the scheduler state survives full pod loss.
3. **Upstream data sources** \u2014 accelerations re-hydrate from the configured connector on startup. RTO is bounded by the time to refresh the largest accelerated dataset.

Recovery procedure:

1. Restore the Kubernetes cluster (or fail over to the secondary region's cluster).
2. Reapply the operator chart and the Spicepod manifests via the GitOps controller.
3. Wait for executors to attach to the existing object-store state, or for `SpicepodSet` accelerations to refresh from upstream.

For RPO-sensitive deployments, run a warm-standby cluster in a second region with the same Spicepod manifests and a cross-region replicated object store.
