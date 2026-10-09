---
description: Multi-active distributed query with Apache Ballista, partition-aware execution, and distributed accelerations for multi-node Spice.ai clusters.
icon: diagram-project
---

# Distributed Query

Spice.ai Enterprise supports distributed query execution, built on [Apache Ballista](https://github.com/apache/datafusion-ballista) and [Apache DataFusion](https://datafusion.apache.org/), for horizontally scaling SQL workloads across multiple nodes. A cluster runs in **multi-active** mode with shared state in an S3-compatible object store, executes queries with **partition-aware** routing across executors, and supports **distributed accelerations** with per-partition data ownership and write-through semantics.

## Architecture

A distributed query cluster is composed of three tiers:

- **Schedulers** — Coordinate query planning, partition discovery and assignment, async job submission, and federated `task_history` aggregation.
- **Executors** — Hold accelerated data for their assigned partitions, execute query stages, perform shuffles, and stream results.
- **Object store** — S3-compatible storage that holds shared cluster state (`cluster.json`, scheduler heartbeats, async job results, and Cayenne acceleration snapshots).

```text
     ┌──────────────────────────────────────┐
     │           Load Balancer              │
     └──────────────────────────────────────┘
                        │
     ┌──────────────────┼──────────────────┐
     ▼                  ▼                  ▼
┌──────────┐     ┌──────────┐     ┌──────────┐
│Scheduler │     │Scheduler │     │Scheduler │◄──► Object Store
└──────────┘     └──────────┘     └──────────┘
     ▲                  ▲                  ▲
     │                  │                  │
┌──────────┐     ┌──────────┐     ┌──────────┐
│ Executor │     │ Executor │     │ Executor │
└──────────┘     └──────────┘     └──────────┘
```

Schedulers register themselves and discover peers through the object store. **Executors are shared across all schedulers** — they are not bound to or owned by any single scheduler. On startup, an executor connects to its bootstrap scheduler, fetches the full scheduler membership list, and then opens a Ballista task `poll_loop` plus a persistent bidirectional `ControlStream` to **every** scheduler over the internal gRPC port (`50052`). It refreshes the membership list every 10 s and adds/drops connections as schedulers join or leave. Any scheduler can dispatch tasks to any executor, and an executor's partition assignment is owned by the cluster (persisted in the object store), not by the scheduler that allocated it.

## Multi-Active High Availability

Multiple schedulers run simultaneously without an external coordinator:

- **Shared state** — Cluster membership and per-table partition metadata are persisted in `cluster.json` in the configured object store. All mutations use **optimistic concurrency control** (ETag / `If-Match`) with up to 8 retry attempts; the schema is versioned (`CLUSTER_STATE_SCHEMA_VERSION = 1`) and incompatible versions are rejected.
- **Heartbeats** — Each scheduler writes a small `heartbeats/{scheduler_id}.json` file independent of `cluster.json` to avoid contention. Heartbeats refresh at one third of the configured TTL (default ~10 s for a 30 s TTL).
- **Discovery** — Every scheduler refreshes its peer map every 5 s by reading `cluster.json` plus live heartbeat files.
- **Reaper** — Schedulers whose last heartbeat exceeds `ttl_ms + 5 s` clock-skew tolerance are evicted on the next reaper tick (jittered ±20 % around `ttl_ms`).
- **Takeover safety** — Each scheduler process generates a fresh `instance_id` (UUID) at startup, so a restarted process can safely reclaim its previous `scheduler_id` without colliding with itself.
- **Executor failover** — Executors maintain a poll loop (Fibonacci backoff, max 5 s) and a `ControlStream` to every scheduler concurrently. If a scheduler dies, the executor's other connections remain live and surviving schedulers continue to drive partition assignment and refresh commands without re-binding the executor.
- **Shared job state** — Job execution graphs are persisted to the object store under `{state_location}/scheduler/graph/{job_id}`, alongside a small compare-and-set governed metadata record holding each job's status and owner. Any scheduler can therefore report the status of a job it did not plan, and if the owning scheduler is lost, another scheduler takes ownership and resumes driving the job to completion instead of failing it.
- **Job ownership fencing** — Ownership is keyed by the owning scheduler's per-process `instance_id`, and a monotonic `epoch` is incremented on every ownership transfer. A scheduler that was presumed dead but later resurfaces observes the higher epoch and declines to continue, so two schedulers never drive the same job.

The object store backing `state_location` **must** support conditional writes. Native AWS S3, S3-compatible stores with `PutIfNotExists`/`PutIfMatch` semantics, and the `file://` backend (for local development) are supported.

## Configuration

### Spicepod (`runtime.scheduler`)

The presence of `runtime.scheduler` activates scheduler role on a node. Setting `--scheduler-address` at launch time activates executor role.

```yaml
runtime:
  scheduler:
    state_location: "s3://my-bucket/spice-cluster/"
    params:
      region: us-east-1
      auth: iam_role                         # iam_role (default) | key
      key: ${secrets:AWS_ACCESS_KEY_ID}
      secret: ${secrets:AWS_SECRET_ACCESS_KEY}
    partition_assignment_interval: "30s"
    max_partition_assignments_per_interval: 100
    max_partitions_per_executor: 1000
    partition_discovery_timeout: "60s"
```

| Field                                    | Default | Description                                                                                                 |
| ---------------------------------------- | ------- | ----------------------------------------------------------------------------------------------------------- |
| `state_location`                         | —       | Object-store URL for shared cluster state. `s3://`, `file://`, or any object store with conditional writes. |
| `params`                                 | —       | Object-store auth/configuration. `auth: iam_role` is the default; explicit keys are sourced from secrets.   |
| `partition_assignment_interval`          | `30s`   | How often the partition discovery + assignment loop runs.                                                   |
| `max_partition_assignments_per_interval` | `100`   | Cap on partitions assigned per cycle to limit churn during scale events.                                    |
| `max_partitions_per_executor`            | `1000`  | Soft cap per executor used by the assignment heuristic.                                                     |
| `partition_discovery_timeout`            | `60s`   | Maximum time to spend discovering partition values from the federated source per cycle.                     |

### CLI flags

The same `spiced` binary runs as scheduler or executor; the role is selected by flag combination.

| Flag                              | Default             | Description                                                                              |
| --------------------------------- | ------------------- | ---------------------------------------------------------------------------------------- |
| `--role`                          | inferred            | `scheduler` or `executor`. Inferred as `executor` when `--scheduler-address` is set.     |
| `--node-bind-address`             | `0.0.0.0:50052`     | Internal gRPC bind address for both roles.                                               |
| `--node-advertise-address`        | required            | Hostname/IP this node advertises to peers. Forms `scheduler_id` as `{advertise}:{port}`. |
| `--scheduler-address`             | required (executor) | URL of the scheduler's internal gRPC service. Scheme inferred from TLS configuration.    |
| `--node-mtls-ca-certificate-file` | —                   | PEM CA used to verify peer mTLS certificates.                                            |
| `--node-mtls-certificate-file`    | —                   | PEM server + client certificate for this node.                                           |
| `--node-mtls-key-file`            | —                   | Private key for the mTLS certificate.                                                    |
| `--allow-insecure-connections`    | `false`             | Disable mTLS. **Development / test only.**                                               |

### Manual launch (non-Kubernetes)

```bash
# Scheduler
spiced --role scheduler \
  --node-mtls-ca-certificate-file ca.pem \
  --node-mtls-certificate-file scheduler.pem \
  --node-mtls-key-file scheduler-key.pem \
  --node-advertise-address scheduler1.cluster.local

# Executor
spiced --role executor \
  --node-mtls-ca-certificate-file ca.pem \
  --node-mtls-certificate-file executor.pem \
  --node-mtls-key-file executor-key.pem \
  --scheduler-address https://scheduler1.cluster.local:50052 \
  --node-advertise-address executor1.cluster.local
```

### Kubernetes (recommended)

Use the [`SpicepodCluster`](../kubernetes/spicepodcluster.md) CRD. The Spice Operator provisions mTLS certificates, child `SpicepodSet` resources for the scheduler and executor pools, services, and PodMonitors automatically. See [High Availability](../production/high-availability.md) for AZ spread, anti-affinity, and `PodDisruptionBudget` guidance.

## Internal gRPC (port 50052)

The internal `ClusterService` gRPC surface is mTLS-protected and never exposed externally. All cluster coordination flows through it:

| RPC                             | Caller                        | Purpose                                                                                                                        |
| ------------------------------- | ----------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| `GetAppDefinition`              | Executor at startup           | Fetches the full Spicepod definition (datasets, catalogs, views, UDFs) so executors do not need a local manifest.              |
| `ExpandSecret`                  | Executor at startup           | Resolves a secret key through the scheduler's secret store.                                                                    |
| `GetSchedulers`                 | Executor at startup           | Returns the list of live scheduler advertise addresses; executor opens a poll loop to each.                                    |
| `AllocateInitialPartitions`     | Executor at startup           | Returns the executor's assigned per-table partition filter expressions (serialized DataFusion `Expr`).                         |
| `ControlStream` (bidirectional) | Executor → Scheduler          | Carries heartbeats and metric responses; receives `UpdatePartitions`, `PollNow`, `RefreshDataset`, and `CancelTasks` commands. |
| `GetTaskHistory`                | Scheduler → peer schedulers   | Federated `runtime.task_history` fan-out across the cluster.                                                                   |
| `GetMetrics`                    | Scheduler → peers / executors | On-demand OTLP metrics collection.                                                                                             |

## Partitioning, Sharding, and Partition-Aware Queries

Distributed Spice clusters shard accelerated tables horizontally across executors using user-declared partition keys. Query planning is partition-aware: each executor only scans the partitions it owns, and the scheduler unions and merges results.

### Declaring partitions

Every accelerated dataset and view in cluster mode **must** declare at least one partition key via `acceleration.partition_by`. Startup fails otherwise with:

> `Accelerated {component_type} '{name}' has no partition keys configured. Add 'partition_by' to its acceleration config to participate in cluster partition assignment.`

Each entry is a SQL expression over the source schema. Entries can be anonymous, named, or column references:

```yaml
datasets:
  - from: s3://lake/sales/
    name: sales
    acceleration:
      enabled: true
      engine: cayenne                       # required for write-through; any engine works for read-only
      partition_by:
        - "YEAR(order_date)"                # anonymous expression -> name `expr0`
        - year: "YEAR(order_date)"          # named expression
        - region: "region"                  # column value as partition key
        - "bucket(100, customer_id)"        # static hash bucketing; no source query required
```

The `bucket(N, col)` function is treated as a **static** partition key: values `0..N-1` are enumerated without querying the source.

### Partition discovery and assignment

Partition assignment is **coordinated through the object store**: `cluster.json` is the single source of truth for which executor owns which partition. Schedulers never assign partitions purely from in-memory state, and executors never own a partition that is not durably committed to the object store first.

The scheduler runs a `PartitionAssignmentTask` on `partition_assignment_interval` (default 30 s):

1. **Discover** — For each accelerated table, runs `SELECT DISTINCT {expressions} FROM {federated_source}` against the source connector (within `partition_discovery_timeout`). Static `bucket(N, col)` keys skip the query.
2. **Diff** — Compares discovered values against `cluster.json` (`accelerations` sub-map). New partitions are recorded as unassigned; stale partitions are removed.
3. **Assign** — Picks executors for unassigned partitions using a greedy minimum set-cover algorithm, respecting `max_partitions_per_executor` and `max_partition_assignments_per_interval`. Tie-breaks are deterministic by executor ID.
4. **Commit** — Writes the assignment (`assigned_executors` field of each partition entry) to `cluster.json` via an OCC conditional write (up to 8 retries). Concurrent schedulers serialize on this write: if two schedulers race on the same partition, one commits first and the other re-reads fresh state and retries, so an executor cannot be assigned the same partition twice.
5. **Notify** — After the object-store commit succeeds, the scheduler pushes an `UpdatePartitions` message over the executor's `ControlStream` so it can immediately materialize the new partitions locally without waiting to re-read `cluster.json`.

### How executors learn their assignments

| Phase                   | Mechanism                                                                                                                                                                                                                            |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Startup / restart**   | Executor calls `AllocateInitialPartitions` (RPC pull) on its bootstrap scheduler, which reads from `cluster.json` and returns the executor's assigned partition filter expressions per table.                                        |
| **Ongoing changes**     | Scheduler commits the change to `cluster.json` (OCC), then pushes `UpdatePartitions` over the `ControlStream`. The executor updates its in-memory `partition_assignments` map and registers/removes local accelerations accordingly. |
| **Authoritative state** | `cluster.json` in the object store. The executor's in-memory map and the scheduler's `PartitionStore` cache are derived views; both are rebuilt from the object store after a process restart.                                       |

### Partition-aware query planning

The DataFusion analyzer rule `PartitionedTableScanRewrite` runs on scheduler-role nodes and rewrites every `TableScan` on an accelerated table into a `UNION ALL` over per-executor FlightSQL scans, pushing down the executor's partition filter and any user predicates:

```text
Before:
  TableScan: sales [filters: status = 'Disputed']

After:
  UNION ALL
    TableScan: sales@executor-1 [filters: status='Disputed' AND year=2024 AND region='us-east']
    TableScan: sales@executor-2 [filters: status='Disputed' AND year=2024 AND region='us-west']
    TableScan: sales@executor-3 [filters: status='Disputed' AND year=2025 AND region='us-east']
```

When the plan contains a `Limit → Sort → Union` pattern (top-K), the `Sort` is pushed into each union leg and the executor returns at most `Limit` rows before the scheduler performs a final merge-sort.

Executor selection uses a greedy minimum set-cover algorithm: pick the executor that covers the most still-required partitions, repeat until coverage is complete, break ties deterministically by executor ID. If any required partition is unassigned, the query fails with:

> `Cannot execute query: N partition(s) not assigned to any executor`

On executors, the `AcceleratedPartitionProvider` resolves partitions to local `TableProvider`s. Row-level partition predicates are not re-evaluated on executors — each executor only holds its own partitions, so filtering happens by ownership.

This rewrite is how [synchronous](#execution-modes) queries reach executor-resident data. Asynchronous queries do not use it — Ballista distributes their scans natively while planning query stages.

### Broadcast joins for small dimension tables

A join between a partitioned fact table and a small dimension table can be federated only at the scans, in which case every fact row travels up to the scheduler, which repartitions and joins centrally. On a large fact table, that transfer dominates query time.

The scheduler instead pushes the join back down to the executors. Each fact-partition executor joins its local fact slice against the full dimension — gathered from its peers — and returns only the joined rows. The rewrite is automatic on scheduler-role nodes; there is no setting to enable.

It applies only when every one of the following holds:

| Condition                     | Requirement                                                                                                                                   |
| ----------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| **Join type**                 | Inner equi-join with no residual filter. Joins using `IS NOT DISTINCT FROM` (NULL-equals-NULL) semantics are left alone.                       |
| **Statistics**                | Row-count statistics are available on both sides. Without them the smaller side cannot be identified and the plan is left unchanged.           |
| **Cost test**                 | The dimension is small enough that broadcasting beats shipping the fact: `dim_rows × executors × 4 < fact_rows`.                               |
| **Dimension size**            | The dimension side is at most 25,000,000 rows, an absolute cap that bounds per-executor memory regardless of the cost test.                    |

Because each fact row lives in exactly one partition and joins independently, the union of the per-partition joins equals the centralized join, so results are unchanged.

### Iceberg catalog scans

Scans of [Iceberg catalog](../../building-blocks/catalogs/iceberg.md) tables are distributed across executors. An Iceberg scan holds a live table handle that cannot be serialized, so the scheduler ships a *recipe* instead — the table reference plus the scan's projection, filters, and limit. Each executor resolves the same table and replays the scan locally, which means catalog credentials are never sent over the wire.

Single-node sessions are unaffected — the scan is planned exactly as it would be without a cluster.

### Partition-aware writes

Write-through `INSERT`, `UPDATE`, `DELETE`, and `MERGE INTO` flow through the same partition-aware Arrow Flight `DoPut` path on the scheduler:

1. The scheduler decodes the inbound `DoPut` schema header.
2. For each `RecordBatch`, it evaluates the table's partition expressions against every row to classify rows into partitions.
3. Rows for partitions already assigned to an executor are streamed via that executor's FlightSQL `DoPut`.
4. Rows whose partition values are **new** are assigned on-the-fly to the least-loaded executor (recorded in `cluster.json` via OCC), then forwarded.
5. All per-executor sub-streams run concurrently. Idle streams emit a keepalive sentinel to avoid the `DoPut` idle timeout (120 s default; override with `SPICE_DO_PUT_IDLE_TIMEOUT_SECS`).

Write-through is currently constrained to the Cayenne accelerator (see [Distributed Accelerations](#distributed-accelerations)).

### Constraints

- Every accelerated table and view in cluster mode must declare `partition_by`.
- Partition expressions must be deterministic SQL over the source schema.
- `bucket(N, col)` is the supported static hash-partition function.
- The scheduler holds **no** accelerated data; partition discovery queries always go to the federated source.
- The DataFusion physical optimizer rule `EnsureSupportedFileScan` rejects plans containing file-scan nodes that cannot be distributed.

## Distributed Accelerations

{% hint style="info" %}
Full reference: [Distributed Accelerations](distributed-accelerations.md).
{% endhint %}

In cluster mode, accelerated data is **sharded** across executors: each executor materializes only the partitions it owns. The scheduler's view of the table is a logical UNION ALL across executors; it never holds row data.

### Engine support

| Engine                | Cluster partition assignment | `write_mode: write_through` | Notes                                                                                             |
| --------------------- | :--------------------------: | :-------------------------: | ------------------------------------------------------------------------------------------------- |
| **Cayenne**                    |     ✓      |     ✓      | Required for write-through. Vortex storage with SQLite metadata. Supports acceleration snapshots.       |
| **Arrow (in-memory)**          |     ✓      |     —      | Read-only. Data is lost on pod restart unless backed by snapshots. `partition_by` shards it per executor. |
| **DuckDB / SQLite / Postgres** |     —      |     —      | Not supported for distributed acceleration; rejected at startup.                                        |

Only Arrow (read-only) and Cayenne can be used for distributed acceleration; any other engine is rejected at startup with `… the 'duckdb' engine is not supported for distributed acceleration. Use 'arrow' (optionally with 'partition_by') or 'cayenne' instead.` Because Arrow is read-only, `write_mode: write_through` requires Cayenne.

### Per-executor sharding

Each partition has a single owning executor (1:1 assignment in `cluster.json`). Executor-local accelerations only contain rows that match their assigned partition filter expressions. Read paths fan out across executors; write paths route by partition key.

### Refresh in cluster mode

- The scheduler is the single source of truth for partition ownership; there is no separate refresh leader election.
- After every source refresh cycle, the `PartitionAssignmentTask` re-runs discovery; new partition values are assigned, and `UpdatePartitions` is pushed to the receiving executor, which materializes the new partition.
- The scheduler can also explicitly issue `RefreshDatasetCommand` over the `ControlStream` to trigger a refresh on a specific executor, which is how API-driven refresh requests are routed.

### Acceleration snapshots (Cayenne)

{% hint style="info" %}
Acceleration snapshots are a **Spice.ai Enterprise** feature. See [Acceleration Snapshots](acceleration-snapshots.md) for the full reference. They are not available in Spice.ai OSS.
{% endhint %}

Cayenne supports object-store-backed acceleration snapshots that integrate naturally with cluster mode, allowing a newly started executor to bootstrap from the shared object store rather than re-fetching from the federated source:

```yaml
acceleration:
  engine: cayenne
  snapshots: enabled                   # enabled | disabled | bootstrap_only | create_only
  snapshots_trigger: refresh_complete  # refresh_complete | time_interval | stream_batches
  snapshots_compaction: enabled
  snapshots_creation_policy: on_change # on_change | always
```

Setting `snapshots: bootstrap_only` is recommended on executors when the source is expensive to scan: executors hydrate from the snapshot at startup but do not produce new snapshots themselves. Cayenne's internal partition metadata supports composite keys (Hive-style `key1=v1/key2=v2/...` paths).

### Sizing

- Size each executor's local NVMe to hold its assigned partitions plus a shuffle working set (rule of thumb: ~25 % of dataset volume).
- The scheduler does not need fast local storage; provision modest CPU / memory and ensure low-latency access to the object store.
- See [Storage](../production/storage.md) for instance-type recommendations.

## Execution Modes

The two modes distribute work differently, and the choice determines whether a query becomes a multi-stage cluster job.

| Mode             | Endpoint             | How work is distributed                                                                                                                                                                                                                                | Notes                                                                                                                                                                                                                                                     |
| ---------------- | -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Synchronous**  | `/v1/sql`, FlightSQL | Scan fan-out only. The scheduler plans and runs the query itself, reading each partition from its owning executor over FlightSQL (see [Partition-aware query planning](#partition-aware-query-planning)). No cluster job, no query stages, no shuffles. | Client waits for the query to complete and receives results directly. Available in any deployment.                                                                                                                                                        |
| **Asynchronous** | `/v1/queries`        | Full stage-based execution. The plan is split into Ballista query stages and dispatched as tasks to executors, which shuffle intermediate results between stages.                                                                                       | Client submits a query and polls a `query_id` for status. Full results are retrieved with pagination via `/v1/queries/{query_id}/results`, or chunk-by-chunk via `/v1/queries/{query_id}/results/chunks/{chunk_index}`. **Cluster (scheduler) mode only.** |

Only asynchronous queries create cluster jobs. A synchronous query against a partitioned accelerated table still reads from executors in parallel, but the joins, aggregations, and sorts above those scans run on the scheduler. Submit workloads whose cost sits above the scan — large joins, wide aggregations, distributed sorts — asynchronously so the cluster executes them across executors.

Submit an async query over HTTP with `POST /v1/queries`, or from the CLI with `spice query`, which submits the query and polls until it completes.

Async queries require `runtime.scheduler.state_location` to be configured. Without it the API returns `503`:

> Async queries API requires distributed mode with `runtime.scheduler.state_location` configured. Start with: `spiced --role scheduler`

Because job state is shared through that object store, an in-flight async query survives the loss of the scheduler that planned it — a surviving scheduler assumes ownership and continues driving it, and the client keeps polling the same `query_id`.

## Observability

All distributed-query metrics are emitted through the OTel `cluster` meter and exposed on the standard Prometheus port (`9090`). The Grafana dashboard shipped with Spice.ai Enterprise (see [Observability](../production/observability.md)) plots the most important signals.

### Shared (scheduler and executor)

| Metric               | Type          | Labels                          |
| -------------------- | ------------- | ------------------------------- |
| `node_status`        | Gauge         | `node_id`, `role`               |
| `node_tasks_total`   | Counter       | `node_id`, `role`, `status`     |
| `node_tasks_active`  | UpDownCounter | `node_id`, `role`               |
| `node_task_failures` | Counter       | `node_id`, `role`, `error_type` |
| `node_task_retries`  | Counter       | `node_id`, `role`               |

### Scheduler

| Metric                                                  | Type                |
| ------------------------------------------------------- | ------------------- |
| `scheduler_active_executors_count`                      | Gauge               |
| `scheduler_count`                                       | Gauge               |
| `scheduler_task_queue_depth`                            | Gauge               |
| `scheduler_task_scheduling_latency_ms`                  | Histogram           |
| `scheduler_stages_total`                                | Counter             |
| `scheduler_stage_duration_ms`                           | Histogram           |
| `scheduler_stage_failures`                              | Counter             |
| `scheduler_stage_retries`                               | Counter             |
| `scheduler_tasks_per_stage`                             | Histogram           |
| `scheduler_planning_duration_ms`                        | Histogram           |
| `scheduler_executor_assignments`                        | Counter             |
| `scheduler_job_queue_depth`                             | Gauge               |
| `scheduler_result_fetch_{bytes,rows,count,duration_ms}` | Counter / Histogram |

### Executor

| Metric                                                        | Type                |
| ------------------------------------------------------------- | ------------------- |
| `executor_tasks_active`                                       | UpDownCounter       |
| `executor_tasks_total`                                        | Counter             |
| `executor_task_failures`                                      | Counter             |
| `executor_task_duration_ms`                                   | Histogram           |
| `executor_memory_available_bytes`                             | Gauge               |
| `executor_task_slots`                                         | Gauge               |
| `executor_shuffle_write_{bytes,rows,duration_ms}`             | Counter / Histogram |
| `executor_shuffle_read_local_{bytes,rows,count,duration_ms}`  | Counter / Histogram |
| `executor_shuffle_read_remote_{bytes,rows,count,duration_ms}` | Counter / Histogram |

### Federated `runtime.task_history`

In cluster mode, `runtime.task_history` is replaced with a federated view. Queries fan out via `GetTaskHistory` to all peer schedulers and via FlightSQL to all connected executors; results are merged and sorted by `start_time DESC`. Every row is annotated with a `node_id` column identifying the originating advertise address. The local non-federated table remains accessible as `runtime.local_task_history` for debugging.

If any peer or executor fails, the federated query fails — there are no partial results.

## Limits and Caveats

| Constraint                                                                                                        | Surface                                                                                 |
| ----------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| Every accelerated dataset and view in cluster mode must declare `partition_by`.                                   | Startup error: `… has no partition keys configured`.                                    |
| Only Arrow (read-only) and Cayenne are supported for distributed acceleration; `write_mode: write_through` requires Cayenne. | Startup error: `… the 'duckdb' engine is not supported for distributed acceleration. Use 'arrow' (optionally with 'partition_by') or 'cayenne' instead.` |
| Two scheduler processes cannot share a `scheduler_id` while one is live.                                          | Startup error: `scheduler id {id} is already registered`.                               |
| All partitions referenced by a query must be assigned to a connected executor at execution time.                  | Runtime error: `Cannot execute query: N partition(s) not assigned to any executor`.     |
| Default `DoPut` idle timeout is 120 s; override with `SPICE_DO_PUT_IDLE_TIMEOUT_SECS`.                            | Long-running write streams must emit data or rely on the keepalive sentinel.            |
| File-scan nodes that cannot be distributed are rejected by the `EnsureSupportedFileScan` physical optimizer rule. | Plan-time error during distributed planning.                                            |
| `cluster.json` schema is versioned (`schema_version = 1`); other versions are rejected.                           | Cluster state from a different runtime major version is not accepted.                   |
| Object stores backing `state_location` must implement conditional writes (ETag / `If-Match`).                     | Required for multi-active correctness.                                                  |

## See also

- [SpicepodCluster CRD reference](../kubernetes/spicepodcluster.md)
- [High Availability](../production/high-availability.md)
- [Storage](../production/storage.md)
- [Observability](../production/observability.md)
- [mTLS Cluster Security](mtls.md)
- [Upgrades — Zero-downtime upgrade for distributed query](../production/upgrades.md#zero-downtime-upgrade-for-distributed-query)
