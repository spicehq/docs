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

Spice supports three modes to refresh/update locally accelerated data from a connected data source. `full` is the default mode. Refer to [Data Refresh](https://docs.spiceai.org/components/data-accelerators/data-refresh) documentation for detailed refresh usage and configuration.

| Mode      | Description                                          | Example                                                          |
| --------- | ---------------------------------------------------- | ---------------------------------------------------------------- |
| `full`    | Replace/overwrite the entire dataset on each refresh | A table of users                                                 |
| `append`  | Append/add data to the dataset on each refresh       | Append-only, immutable datasets, such as time-series or log data |
| `changes` | Apply incremental changes                            | Customer order lifecycle table                                   |

`refresh_mode: changes` streams committed inserts, updates, and deletes from the source's own changelog. See [Database Replication and CDC](../database-replication-and-cdc.md) for supported sources and configuration.

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

