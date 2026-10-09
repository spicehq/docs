---
description: Object-store-backed snapshot, bootstrap, and recovery for accelerated datasets in Spice.ai Enterprise.
icon: camera
---

# Acceleration Snapshots

Acceleration snapshots persist accelerated dataset state to an object store so that a Spice runtime can bootstrap a new replica or executor without re-fetching from the federated source. Snapshots dramatically reduce cold-start time, support fast scale-out, and provide a simple disaster-recovery path for accelerated state.

{% hint style="info" %}
Acceleration snapshots are a **Spice.ai Enterprise** feature. They are not available in Spice.ai OSS.
{% endhint %}

## How it works

1. The runtime writes the accelerated dataset to an object store (S3-compatible, Azure Blob, GCS, or any [object store with conditional writes](https://spiceai.org/docs/components/data-connectors/s3)).
2. On startup, an accelerated dataset that opts into snapshots reads the most recent snapshot from the configured location and hydrates its local accelerator before serving queries.
3. While running, snapshots are produced according to the dataset's configured trigger and creation policy. Older snapshots can be compacted to bound storage cost.
4. In a [`SpicepodCluster`](../kubernetes/spicepodcluster.md), each executor reads the snapshot for the partitions it owns and writes new snapshots after refreshes complete on the partitions it is responsible for.

| Engine                   | Snapshot support                                   | Notes                                                                                                  |
| ------------------------ | -------------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| **DuckDB**               | ✓ Bootstrap + create                                | File-mode; snapshot is the DuckDB file uploaded to object storage.                                     |
| **SQLite**               | ✓ Bootstrap + create                                | File-mode; snapshot is the SQLite file uploaded to object storage.                                     |
| **Cayenne**              | ✓ Bootstrap + create (recommended for distributed) | Vortex storage with SQLite metadata; integrates natively with `SpicepodCluster` partitioned executors. |
| **Arrow (in-memory)**    | —                                                  | Re-fetched from source on restart.                                                                     |
| **Postgres (external)**  | —                                                  | External database is itself the durable store.                                                         |

## Configuration

Snapshots are configured in two places:

1. **Top-level `snapshots`** — declares the snapshot store location, credentials, and global behavior.
2. **Per-dataset `acceleration.snapshots*`** — opts the dataset into snapshots and chooses behavior, trigger, and compaction.

```yaml
# Top-level snapshot store configuration
snapshots:
  enabled: true                         # default: true
  location: "s3://my-bucket/spice/snapshots/"
  bootstrap_on_failure_behavior: warn   # warn | retry | fallback
  params:
    region: us-east-1
    s3_auth: iam_role                   # default for snapshots; override with `key`
    # s3_key: ${secrets:AWS_ACCESS_KEY_ID}
    # s3_secret: ${secrets:AWS_SECRET_ACCESS_KEY}

datasets:
  - from: s3://lake/sales/
    name: sales
    acceleration:
      enabled: true
      engine: cayenne
      partition_by:
        - region: "region"
      # Per-dataset opt-in
      snapshots: enabled                  # disabled | enabled | bootstrap_only | create_only
      snapshots_trigger: refresh_complete # refresh_complete | time_interval | stream_batches
      snapshots_trigger_threshold: "10m"  # required when trigger is time_interval / stream_batches
      snapshots_compaction: enabled       # disabled (default) | enabled
      snapshots_creation_policy: on_change # on_change (default) | always
      snapshots_reset_expiry_on_load: disabled # disabled (default) | enabled
```

### Top-level `snapshots`

| Field                            | Type                                | Default     | Description                                                                                                          |
| -------------------------------- | ----------------------------------- | ----------- | -------------------------------------------------------------------------------------------------------------------- |
| `enabled`                        | bool                                | `true`      | Global on/off switch. When `false`, no dataset can use snapshots regardless of per-dataset config.                   |
| `location`                       | string                              | —           | Object-store URL of the snapshot folder (e.g. `s3://bucket/spice/snapshots/`, `abfs://…`, `gs://…`).                |
| `bootstrap_on_failure_behavior`  | `warn` \| `retry` \| `fallback`     | `warn`      | What to do if snapshot load fails. `warn` continues with empty acceleration; `retry` retries indefinitely; `fallback` tries older snapshots. |
| `params`                         | object                              | —           | Object-store auth/configuration. For S3, defaults to `s3_auth: iam_role`; explicit keys may be sourced from secrets. |

### Per-dataset `acceleration` snapshot fields

| Field                            | Type                                                                          | Default      | Description                                                                                                  |
| -------------------------------- | ----------------------------------------------------------------------------- | ------------ | ------------------------------------------------------------------------------------------------------------ |
| `snapshots`                      | `disabled` \| `enabled` \| `bootstrap_only` \| `create_only`                  | `disabled`   | Per-dataset opt-in. `enabled` does both bootstrap and create. `bootstrap_only` only loads. `create_only` only writes. |
| `snapshots_trigger`              | `refresh_complete` \| `time_interval` \| `stream_batches`                     | `refresh_complete` | When to attempt to write a snapshot.                                                                  |
| `snapshots_trigger_threshold`    | duration / count string                                                       | —            | For `time_interval` (e.g. `"10m"`) and `stream_batches` (e.g. `"1000"`) triggers.                            |
| `snapshots_compaction`           | `disabled` \| `enabled`                                                       | `disabled`   | When `enabled`, older snapshots are compacted/garbage-collected.                                             |
| `snapshots_creation_policy`      | `on_change` \| `always`                                                       | `on_change`  | `on_change` skips creating a new snapshot when the dataset hasn't changed since the last snapshot.           |
| `snapshots_reset_expiry_on_load` | `disabled` \| `enabled`                                                       | `disabled`   | When `enabled`, loading a snapshot resets the dataset's expiry/retention clock — useful for cold standby replicas. |

## Behavior modes

| Mode             | Bootstrap from snapshot? | Write new snapshots? | Typical use                                                                                                |
| ---------------- | :----------------------: | :------------------: | ---------------------------------------------------------------------------------------------------------- |
| `disabled`       |            —             |          —           | Snapshots off for this dataset (default).                                                                  |
| `enabled`        |            ✓             |          ✓           | Standard production setting: bootstrap fast, keep the snapshot up to date.                                 |
| `bootstrap_only` |            ✓             |          —           | Read replicas / scaled-out executors that should hydrate from a shared snapshot but never overwrite it.    |
| `create_only`    |            —             |          ✓           | A "primary" replica that produces snapshots for other replicas to consume.                                  |

A common topology in `SpicepodCluster` is one `create_only` (or `enabled`) executor per partition writing snapshots, with newly added executors using `bootstrap_only` to come online quickly.

## Triggers

| Trigger             | Threshold required | Description                                                                                                  |
| ------------------- | :----------------: | ------------------------------------------------------------------------------------------------------------ |
| `refresh_complete`  |         —          | Default. A snapshot is attempted after each successful dataset refresh.                                       |
| `time_interval`     |         ✓          | A snapshot is attempted on a timer (`snapshots_trigger_threshold: "10m"`).                                    |
| `stream_batches`    |         ✓          | For streaming sources, a snapshot is attempted every N consumed batches (`snapshots_trigger_threshold: "1000"`). |

`snapshots_creation_policy: on_change` (default) suppresses snapshots when there is no change to commit; pair it with `time_interval` or `stream_batches` to bound snapshot frequency.

## Failure handling on bootstrap

`bootstrap_on_failure_behavior` controls what happens if the runtime cannot read the most recent snapshot:

- **`warn`** — Log a warning and continue with an empty acceleration. The next refresh will repopulate it from the federated source. Suitable when the source is fast and snapshots are an optimization.
- **`retry`** — Retry loading the newest snapshot indefinitely. Suitable for replicas that should not serve stale-empty data.
- **`fallback`** — Try progressively older snapshots until one loads successfully. Suitable for resilience against a corrupt or partially written latest snapshot.

## Distributed clusters

In a [`SpicepodCluster`](../kubernetes/spicepodcluster.md):

- The snapshot location should point to the same object store used for cluster shared state (or a dedicated bucket) so all schedulers and executors share a single view.
- Each executor writes snapshots only for the partitions it owns; reads target the same partition layout (Hive-style `key1=v1/key2=v2/...`).
- New executors joining the cluster receive their partition assignments via `AllocateInitialPartitions` (see [Distributed Query](distributed-query.md#how-executors-learn-their-assignments)) and then bootstrap each owned partition from the snapshot store, avoiding a federated re-scan.
- Setting `snapshots: bootstrap_only` on executors prevents accidental dual-writers when more than one executor temporarily believes it owns a partition during a transition; the scheduler-confirmed owner uses `enabled` or `create_only`.

## Observability

Acceleration snapshots emit Prometheus / OTLP metrics on every node:

| Metric                                                  | Type      | Description                                                                                  |
| ------------------------------------------------------- | --------- | -------------------------------------------------------------------------------------------- |
| `dataset_acceleration_snapshot_bootstrap_bytes`         | gauge     | Bytes downloaded when bootstrapping the acceleration from a snapshot.                        |
| `dataset_acceleration_snapshot_bootstrap_checksum`      | gauge     | Checksum of the snapshot downloaded during bootstrap (emitted with `checksum` attribute).    |
| `dataset_acceleration_snapshot_bootstrap_duration_ms`   | counter   | Time in ms taken to download the snapshot used to bootstrap acceleration.                    |
| `dataset_acceleration_snapshot_failure_count`           | counter   | Number of failures encountered while writing snapshots.                                      |
| `dataset_acceleration_snapshot_write_bytes`             | gauge     | Bytes written for the most recent snapshot.                                                  |
| `dataset_acceleration_snapshot_write_checksum`          | gauge     | Checksum of the most recent snapshot write (emitted with `checksum` attribute).              |
| `dataset_acceleration_snapshot_write_duration_ms`       | histogram | Time in ms taken to write the latest snapshot to object storage.                              |
| `dataset_acceleration_snapshot_write_timestamp`         | gauge     | Unix timestamp (seconds) when the most recent snapshot write completed.                      |

## Production checklist

- [ ] Snapshot bucket has versioning enabled and a lifecycle policy that retains a documented number of snapshot generations.
- [ ] `bootstrap_on_failure_behavior: fallback` is used on critical datasets that cannot tolerate empty starts.
- [ ] Object-store credentials use IRSA / workload identity, not long-lived access keys.
- [ ] An alert is configured on `dataset_acceleration_snapshot_failure_count` greater than zero over a rolling 15-minute window.
- [ ] An alert is configured on stale `dataset_acceleration_snapshot_write_timestamp` for datasets that should snapshot regularly.
- [ ] In `SpicepodCluster`, the snapshot bucket is in the same region as the runtime nodes to minimise bootstrap latency.

## See also

- [Distributed Query — Acceleration snapshots (Cayenne)](distributed-query.md#acceleration-snapshots-cayenne)
- [SpicepodCluster CRD reference](../kubernetes/spicepodcluster.md)
- [Storage](../production/storage.md)
- [Observability](../production/observability.md)
