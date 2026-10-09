---
icon: bolt
description: Configure local acceleration for datasets in Spice for faster queries (test)
---

# Data Acceleration

Datasets can be locally accelerated by the Spice runtime, pulling data from any [Data Connector](https://docs.spiceai.org/components/data-connectors) and storing it locally in a [Data Accelerator](https://docs.spiceai.org/components/data-accelerators) for faster access. The data can be kept up-to-date in real-time or on a refresh schedule, ensuring users always have the latest data locally for querying.

### Supported Data Accelerators <a href="#example" id="example"></a>

Dataset acceleration is enabled by setting the `acceleration` configuration. Spice currently supports In-Memory Arrow, DuckDB, SQLite, PostgreSQL as accelerators. For engine specific configuration, see [Data Accelerator Documentation](https://docs.spiceai.org/components/data-accelerators)

#### Example - Locally Accelerating taxi\_trips with Arrow Accelerator <a href="#example" id="example"></a>

```yaml
datasets:
  - from: spice.ai/spiceai/quickstart/datasets/taxi_trips
    name: taxi_trips
    acceleration:
      enabled: true
      refresh_mode: full
      refresh_check_interval: 10s
```

### Refresh Modes <a href="#refresh-modes" id="refresh-modes"></a>

Spice supports several modes to refresh/update locally accelerated data from a connected data source. `full` is the default mode. Refer to [Data Refresh](https://spiceai.org/docs/features/data-acceleration/data-refresh) documentation for detailed refresh usage and configuration.

| Mode      | Description                                          | Example                                                          |
| --------- | ---------------------------------------------------- | ---------------------------------------------------------------- |
| `full`    | Replace/overwrite the entire dataset on each refresh | A table of users                                                 |
| `append`  | Append/add data to the dataset on each refresh       | Append-only, immutable datasets, such as time-series or log data |
| `changes` | Apply incremental changes                            | Customer order lifecycle table                                   |
| `caching` | Cache HTTP responses in the accelerator, keyed by request metadata | An HTTP API read through Spice                            |

`refresh_mode: changes` streams committed inserts, updates, and deletes from the source's own changelog. See [Database Replication and CDC](../database-replication-and-cdc.md) for supported sources and configuration.

`refresh_mode: caching` applies to [HTTP(S) datasets](https://spiceai.org/docs/components/data-connectors/https). A query that identifies a request (path, query, and body) is served from the accelerator while the cached entry is inside `caching_ttl`. When `caching_ttl` is omitted it defaults to `30s`. After that fresh window, `caching_stale_while_revalidate_ttl` is how long Spice may still return the accelerator copy while it refreshes the origin in the background. See [Caching refresh mode](https://spiceai.org/docs/features/data-acceleration/refresh-modes/caching) and [Read-through cache](https://spiceai.org/docs/use-cases/caching/read-through-cache).

#### Availability-first HTTP caching <a href="#availability-first-http-caching" id="availability-first-http-caching"></a>

When the goal is to prefer the origin and use the accelerator only if the origin fails, set a zero fresh window and a zero stale-while-revalidate window, and enable stale-if-error:

```yaml
datasets:
  - from: https://api.example.com
    name: api_cache
    params:
      file_format: json
      allowed_request_paths: '/v1/**'
      request_query_filters: enabled
    acceleration:
      enabled: true
      refresh_mode: caching
      params:
        caching_ttl: 0s
        caching_stale_while_revalidate_ttl: 0s
        caching_stale_if_error: enabled # or a finite duration, such as 10m
        caching_max_size: 512MiB
```

`caching_ttl: 0s` is a fresh window of length zero, so a stored entry is already expired on the next keyed read. `caching_stale_while_revalidate_ttl: 0s` closes the window in which Spice would return that expired accelerator copy while the origin is still being refreshed. Together they send the read to the origin whenever the origin can answer. The accelerator is consulted when the origin fails (including a `429` or `5xx` after the connector's own retries) and `caching_stale_if_error` allows the fallback.

A positive `caching_stale_while_revalidate_ttl` still serves the accelerator for that long after the fresh window, including when `caching_ttl` is `0s`. Set both durations to `0s` for the origin-first pattern. The fallback is available only after a successful origin response has been stored. A query with no request filters reads the accelerator as stored and does not run this origin check.

`caching_stale_if_error: enabled` serves that fallback with no age limit, and the expiry sweep keeps the entry for a failing origin. Pair `enabled` with `caching_max_size`, `caching_max_items`, or a retention rule (`retention_period` or `retention_sql`) so the accelerator stays bounded. A finite duration such as `caching_stale_if_error: 10m` serves the fallback only while the entry is at most that far past `caching_ttl`, and the sweep can evict entries older than that on its own.

#### Example - Accelerate with arrow accelerator under full refresh mode <a href="#example" id="example"></a>

```yaml
datasets:
  - from: databricks:taxi_trips
    name: taxi_trips
    acceleration:
      refresh_mode: full
      refresh_check_interval: 10m
```

### Incremental Ingestion <a href="#incremental-ingestion" id="incremental-ingestion"></a>

For sources that expose a monotonically-increasing version column (e.g. `updated_at`, `lastUpdateTime`), Spice can incrementally ingest only new or modified records using `time_column` together with `refresh_mode: append` and a `refresh_check_interval`. Combined with `retention_period`, old records are automatically evicted so the accelerated replica stays bounded in size.

**Behavior**

- **Initial load**: Spice loads all records from the source where `time_column > now() - refresh_data_window`.
- **Incremental refresh**: On each `refresh_check_interval`, Spice queries the source for records where `time_column` is newer than the most recent value already in the accelerated store, and appends them. If `primary_key` is set, matching rows are upserted instead of duplicated.
- **Overlap window**: Use `refresh_append_overlap` to widen the incremental query to `time_column > max(time_column) - refresh_append_overlap`. This re-reads a small trailing window on every refresh to tolerate clock skew between the source and the runtime, and to pick up late-arriving writes whose `time_column` is slightly behind the refresh boundary. Combined with `primary_key` upserts, any rows re-read in the overlap are deduplicated rather than duplicated — so no records are lost near the refresh boundary and no duplicates are introduced.
- **Retention**: On each `retention_check_interval`, rows where `time_column` is older than `retention_period` are removed from the accelerated store, bounding storage and aging out data that is no longer needed.

**Handling deletes**

For sources that do not emit a change feed (e.g. HTTP APIs), the recommended pattern is **soft deletes**: the source marks removed records with a `deleted` flag (and bumps `time_column`). The incremental refresh picks up the tombstone via the normal append path, the upsert replaces the live row with its soft-deleted version, and `retention_period` eventually evicts it from the accelerated store. Queries should filter `WHERE deleted = false` (or use a [view](../../building-blocks/views/)) to hide soft-deleted rows. This avoids the cost of periodic full snapshots.

If soft deletes are not available, schedule a periodic `refresh_mode: full` snapshot to reconcile hard deletes by atomically replacing the accelerated contents. For sources that emit a complete change feed (e.g. Debezium, Kafka), use [`refresh_mode: changes`](../../building-blocks/data-connectors/debezium.md) instead to propagate inserts, updates, and deletes in real time.

#### Example - Incrementally ingest the last 90 days of GitHub pull requests <a href="#example" id="example"></a>

Checks for new and updated records every 15 minutes, with a 5-minute overlap to cover clock skew and late arrivals. Rows updated in the source are upserted via `primary_key` + `on_conflict: upsert`, and soft-deleted rows (`deleted_at IS NOT NULL`) are evicted by `retention_sql` in addition to the time-based `retention_period`:

```yaml
datasets:
  - from: github:github.com/spiceai/spiceai/pulls
    name: pulls
    params:
      github_token: ${secrets:GITHUB_TOKEN}
      github_query_mode: search
    time_column: updated_at
    acceleration:
      enabled: true
      refresh_mode: append
      refresh_check_interval: 15m
      refresh_append_overlap: 5m
      refresh_data_window: 90d
      primary_key: id
      on_conflict:
        id: upsert
      retention_check_enabled: true
      retention_check_interval: 1h
      retention_period: 90d
      retention_sql: DELETE FROM pulls WHERE deleted_at IS NOT NULL
```

### Indexes

Database indexes are essential for optimizing query performance. Configure indexes for accelerators via `indexes` field. For detailed configuration, refer to the [index](https://docs.spiceai.org/features/data-acceleration/indexes) documentation.

#### Example - Configure indexes with SQLite Accelerator <a href="#example" id="example"></a>

```yaml
datasets:
  - from: databricks:taxi_trips
    name: taxi_trips
    acceleration:
      enabled: true
      engine: sqlite
      indexes:
        number: enabled # Index the `number` column
        '(hash, timestamp)': unique # Add a unique index with a multicolumn key comprised of the `hash` and `timestamp` columns
```

## Constraints

Constraints enforce data integrity in a database. Spice supports constraints on locally accelerated tables to ensure data quality and configure behavior for data updates that violate constraints.&#x20;

Constraints are specified using [column references](https://docs.spiceai.org/#column-references) in the Spicepod via the `primary_key` field in the acceleration configuration. Additional unique constraints are specified via the [`indexes`](https://docs.spiceai.org/features/data-acceleration/indexes) field with the value `unique`. Data that violates these constraints will result in a [conflict](https://docs.spiceai.org/#handling-conflicts). For constraints configuration details, visit [Constraints Documentation](https://docs.spiceai.org/features/data-acceleration/constraints).

#### Example - Configure primary key constraints  with SQLite Accelerator <a href="#example" id="example"></a>

```yaml
datasets:
  - from: databricks:taxi_trips
    name: taxi_trips
    acceleration:
      enabled: true
      engine: sqlite
      primary_key: hash # Define a primary key on the `hash` column
      indexes:
        '(number, timestamp)': unique # Add a unique index with a multicolumn key comprised of the `number` and `timestamp` columns
```

