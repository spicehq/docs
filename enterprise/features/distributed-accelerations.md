---
description: Partition-sharded accelerated datasets across executors with write-through semantics in Spice.ai Enterprise.
icon: grid-2-plus
---

# Distributed Accelerations

In a [distributed query cluster](distributed-query.md), Spice.ai Enterprise shards each accelerated dataset and view across executors so that every executor materialises and serves only the partitions it owns. The scheduler holds **no row data**; it presents a logical `UNION ALL` of all executors and routes reads and writes by partition key.

{% hint style="info" %}
Distributed accelerations are a **Spice.ai Enterprise** feature. They require a `SpicepodCluster` with at least one scheduler and one executor. They are not available in Spice.ai OSS.
{% endhint %}

## Why partition?

A single-replica accelerated dataset is bounded by the memory and disk of one node. Partitioning the acceleration across many executors:

- Scales accelerated capacity linearly with the number of executors.
- Localises hot data on NVMe-backed executors and keeps the scheduler stateless.
- Bounds query fan-out — only executors holding a relevant partition are scanned.
- Enables independent refresh, snapshot, and recovery per partition.

## Declaring partitions

Every accelerated dataset and view in cluster mode **must** declare at least one partition key via `acceleration.partition_by`. Startup fails otherwise:

> `Accelerated {component_type} '{name}' has no partition keys configured. Add 'partition_by' to its acceleration config to participate in cluster partition assignment.`

```yaml
datasets:
  - from: postgres:public.events
    name: events
    acceleration:
      enabled: true
      engine: cayenne
      mode: file
      refresh_mode: changes
      partition_by:
        - "bucket(100, customer_id)"        # static hash bucketing — no source query
        - { region: "region" }              # named column-value partition
```

`partition_by` accepts each entry as either:

- A plain expression string (auto-named `expr0`, `expr1`, …), e.g. `"YEAR(created_at)"` or `"bucket(64, user_id)"`.
- A single-entry mapping `{ name: expression }`, e.g. `{ year: "YEAR(created_at)" }`.

Multiple keys yield the cartesian product of values; each combination is one partition.

### Static vs. dynamic discovery

For each accelerated table, the scheduler discovers the set of partition values exactly once per refresh cycle. There are two paths:

- **Static** — `bucket(N, col)` is recognised at config time and expands to values `0, 1, …, N-1` without contacting the source. `N` must be a positive integer literal up to **1,000,000**.
- **Dynamic** — Any other expression is resolved by running `SELECT DISTINCT {expressions} FROM {federated_source}` against the source connector, bounded by `runtime.scheduler.partition_discovery_timeout` (default `60s`).

The `bucket(N, col)` UDF is a deterministic, hash-based bucketing function (`ahash` with a fixed seed) and is evaluable both in the scheduler refresh context and on each executor — so the same row always lands on the same bucket.

## Engine support

| Engine                | Cluster partition assignment | `write_mode: write_through` | Notes                                                                                             |
| --------------------- | :--------------------------: | :-------------------------: | ------------------------------------------------------------------------------------------------- |
| **Cayenne**                    |     ✓      |     ✓      | Required for write-through. Vortex storage with SQLite metadata. Supports [acceleration snapshots](acceleration-snapshots.md). |
| **Arrow (in-memory)**          |     ✓      |     —      | Read-only. Data is lost on pod restart unless backed by snapshots. `partition_by` shards it per executor.                     |
| **DuckDB / SQLite / Postgres** |     —      |     —      | Not supported for distributed acceleration; rejected at startup.                                                              |

Only Arrow (read-only) and Cayenne can be used for distributed acceleration; any other engine is rejected at startup with `… the 'duckdb' engine is not supported for distributed acceleration. Use 'arrow' (optionally with 'partition_by') or 'cayenne' instead.` Because Arrow is read-only, `write_mode: write_through` requires Cayenne.

## Per-executor sharding

Each partition has a single owning executor (1:1 assignment recorded in `cluster.json`). Consequences:

- Executor-local accelerations only contain rows that match their assigned partition filter expressions — no executor stores the full dataset.
- Read paths fan out over the executors that own the partitions touched by the query (statically pruned by `partition_by` predicates whenever possible).
- Write paths route by partition key; the scheduler computes the owning executor and forwards the write.

The scheduler never holds row data; the DataFusion physical optimiser rule `EnsureSupportedFileScan` rejects scheduler plans containing file-scan nodes that cannot be distributed.

## Partition assignment

Partition assignment lives in the shared `cluster.json` document in the cluster object store and is reconciled by the scheduler:

1. **Discover** — Run static or dynamic discovery for every accelerated table.
2. **Diff** — Compare the discovered set against the current partitions in `cluster.json` to compute new and removed partitions.
3. **Assign** — Assign new partitions to executors using a soft-balanced fill (newest partitions first). At most `runtime.scheduler.max_partition_assignments_per_interval` are committed per cycle (default `100`); each executor is capped at `runtime.scheduler.max_partitions_per_executor` (default `1000`).
4. **Notify** — Push `UpdatePartitions` over the cluster `ControlStream` to each receiving executor; the executor materialises the new partition by scanning its assigned filter from the federated source.

```yaml
runtime:
  scheduler:
    state_location: "s3://my-bucket/spice/cluster/"
    partition_assignment_interval: "30s"
    max_partition_assignments_per_interval: 100
    max_partitions_per_executor: 1000
    partition_discovery_timeout: "60s"
```

| Field                                     | Default | Description                                                                                |
| ----------------------------------------- | ------- | ------------------------------------------------------------------------------------------ |
| `state_location`                          | —       | Object-store URL of the shared cluster state and snapshots root.                            |
| `partition_assignment_interval`           | `30s`   | Cadence of periodic partition reconciliation across all accelerated tables.                |
| `max_partition_assignments_per_interval`  | `100`   | Soft cap on assignments committed per cycle (uncapped on on-demand reconciliation).        |
| `max_partitions_per_executor`             | `1000`  | Soft per-executor partition cap used to spread load.                                       |
| `partition_discovery_timeout`             | `60s`   | Time budget for `SELECT DISTINCT` partition discovery against the federated source.        |

Two reconciliation paths share this state:

- **Periodic** (`reconcile_all`) — Every `partition_assignment_interval`, with the per-cycle cap applied.
- **On-demand** (`reconcile_table`) — Triggered before a refresh forwarded over `ControlStream`, uncapped so newly-discovered partitions are not stranded for a refresh that would target them.

Assignments are committed to `cluster.json` first under optimistic concurrency control (OCC), then pushed to executors. Concurrent schedulers are safe: at most one assignment per partition wins.

## Refresh in cluster mode

- The scheduler is the single source of truth for partition ownership; there is no separate refresh leader election.
- After every source refresh cycle, partition reconciliation re-runs discovery; new partition values are assigned and `UpdatePartitions` is pushed to the receiving executor, which materialises the new partition.
- The scheduler can also explicitly issue `RefreshDataset` over the `ControlStream` to trigger a refresh on a specific executor, which is how API-driven refresh requests are routed in cluster mode.

`refresh_mode: changes` (CDC / WAL replication) is fully supported in cluster mode and is the recommended setting for write-through accelerations to keep accelerator state close to the source.

## Write paths

```yaml
acceleration:
  enabled: true
  engine: cayenne
  write_mode: write_through    # write_through (default) | write_back
```

| Mode               | Semantics                                                                                                                                                                |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **`write_through`** (default) | Writes commit synchronously to the federated source. Acceleration state catches up via the configured refresh mechanism. **Cluster mode supports `write_through` only on Cayenne.** |
| **`write_back`**   | Writes commit to the local accelerator first; the source is updated asynchronously. Requires `replication.enabled: true`. Cluster `write_back` is a future extension.    |

In cluster mode, the scheduler computes the owning executor for each row from `partition_by`, splits the incoming Arrow Flight stream into per-executor batches, and forwards each batch to its executor with the same `partition_by` filters applied.

## Acceleration snapshots

Cayenne integrates with [Acceleration Snapshots](acceleration-snapshots.md) so that a newly added executor can hydrate its assigned partitions from object storage instead of re-scanning the federated source:

```yaml
acceleration:
  engine: cayenne
  snapshots: enabled                   # disabled | enabled | bootstrap_only | create_only
  snapshots_trigger: refresh_complete  # refresh_complete | time_interval | stream_batches
  snapshots_compaction: enabled
  snapshots_creation_policy: on_change # on_change | always
```

Recommended cluster pattern: set `snapshots: enabled` on owning executors (writes the canonical snapshot for their partitions) and `bootstrap_only` on read-replica executors so they never overwrite a snapshot they did not produce.

## Sizing

- Provision each executor's local NVMe to hold its assigned partitions plus a shuffle working set (rule of thumb: ~25% of dataset volume).
- The scheduler does **not** need fast local storage; provision modest CPU / memory and ensure low-latency access to the cluster object store.
- `max_partitions_per_executor` is a soft target — keep partition counts much smaller than this in steady state to leave headroom for rebalancing.
- Partition cardinality should comfortably exceed the executor count so that work spreads evenly. With `bucket(N, col)`, `N` is typically `4×–32×` the executor count.
- See [Storage](../production/storage.md) for instance-type recommendations.

## Observability

Distributed accelerations emit metrics through the cluster meter alongside the standard [acceleration metrics](../../cloud/api/metrics.md):

| Metric                                  | Where      | Description                                                                  |
| --------------------------------------- | ---------- | ---------------------------------------------------------------------------- |
| `scheduler_executor_assignments`        | Scheduler  | Counter of partition assignments committed.                                  |
| `scheduler_active_executors_count`      | Scheduler  | Number of executors currently registered.                                    |
| `node_status`                           | Both       | Per-node liveness for schedulers and executors.                              |
| `dataset_acceleration_refresh_*`        | Executor   | Standard acceleration refresh metrics, emitted per partition owner.          |
| `dataset_acceleration_snapshot_*`       | Executor   | Snapshot bootstrap / write metrics. See [Acceleration Snapshots](acceleration-snapshots.md). |

## Constraints and edge cases

| Constraint                                                                                                          | Effect                                                                                  |
| ------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| Every accelerated table and view in cluster mode must declare `partition_by`.                                       | Startup error: `… has no partition keys configured`.                                    |
| `bucket(N, col)` requires a positive integer literal `N` ≤ `1_000_000`.                                              | Otherwise the scheduler falls through to dynamic discovery, which is unlikely to match.  |
| `write_mode: write_through` is only supported on Cayenne.                                                            | Startup error on other engines.                                                          |
| The cluster object store must support conditional writes (S3, GCS, Azure Blob).                                      | Local filesystem is rejected; OCC requires `PutMode::Update`.                            |
| Partition cardinality below the executor count leaves executors idle; very high cardinality inflates `cluster.json`. | Aim for 4×–32× executors-worth of partitions; tune via `bucket(N, …)` or coarser keys. |

## See also

- [Distributed Query](distributed-query.md) — multi-active execution model and RPC surface.
- [Acceleration Snapshots](acceleration-snapshots.md) — object-store-backed bootstrap for partitioned executors.
- [SpicepodCluster CRD](../kubernetes/spicepodcluster.md) — Kubernetes deployment.
- [Storage](../production/storage.md) — sizing and instance-type guidance.
