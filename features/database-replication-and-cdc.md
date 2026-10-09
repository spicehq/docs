---
icon: arrows-rotate
description: Replicate committed changes from operational databases into an accelerated, query-ready replica using change data capture
---

# Database Replication and CDC

**Database replication** keeps an accelerated dataset continuously in step with its source by reading the source database's own changelog. Committed inserts, updates, and deletes are applied to the local replica within seconds, with no batch window and no external pipeline.

The mechanism is **change data capture (CDC)**: rather than re-reading the source table on a schedule, Spice consumes the stream of changes the database already produces for its own recovery and replication — the PostgreSQL write-ahead log, the MySQL binary log, a MongoDB change stream, a DynamoDB stream — and applies each change to the accelerator as it commits.

Replication is enabled by setting `refresh_mode: changes` on an accelerated dataset.

{% hint style="info" %}
`changes` is one of three [refresh modes](data-acceleration/README.md#refresh-modes). Use `full` to replace a dataset on each refresh, `append` for immutable or time-series data, and `changes` to mirror a mutable source that emits a change feed.
{% endhint %}

### Why replicate

Running analytical queries against a production database competes with transaction processing for the same connections, buffer pool, and CPU. The usual alternatives each carry a cost:

* **ETL pipelines** add latency measured in minutes or hours, plus the infrastructure to build, schedule, and monitor them.
* **Read replicas** relieve the primary but run the same row-oriented engine, so analytical scans remain slow.
* **HTAP databases** require migrating off the existing system and couple transactional and analytical failure domains.

CDC-based replication into a columnar accelerator avoids all three. The operational database keeps serving transactions, analytical load lands on separate storage and compute, and the replica stays seconds behind rather than hours.

For the architecture built on this capability, see [Analytics Replica](../use-cases/analytics-replica.md).

### Supported sources

| Source | Mechanism | Configuration |
| ------ | --------- | ------------- |
| [PostgreSQL](../building-blocks/data-connectors/postgres.md) | Logical replication from the write-ahead log | `refresh_mode: changes` |
| [MySQL](../building-blocks/data-connectors/mysql.md) | Binary log (binlog) replication | `refresh_mode: changes` |
| [MongoDB](../building-blocks/data-connectors/mongodb.md) | Change streams on the source collection | `refresh_mode: changes` |
| [DynamoDB](../building-blocks/data-connectors/dynamodb.md) | DynamoDB Streams | `refresh_mode: changes` |
| [Apache Kafka](../building-blocks/data-connectors/kafka.md) | Event stream consumption | `refresh_mode: append` |
| [Debezium](../building-blocks/data-connectors/debezium.md) | Debezium change events over Kafka | `refresh_mode: changes` |
| `cdc` | Debezium change events posted directly to Spice, without Kafka | `refresh_mode: changes` |

{% hint style="info" %}
Sources without a native Spice change feed — SQL Server, for example — replicate through [Debezium](../building-blocks/data-connectors/debezium.md).
{% endhint %}

A `cdc` dataset is the Debezium path without the message bus. Any Debezium source plugin posts its change events straight to Spice at `POST /v1/datasets/{name}/cdc`, in JSON or Avro, so a replica needs no Kafka cluster to sit between the plugin and the runtime. The `debezium` connector, which consumes the same events over Kafka, is unchanged.

Unlike the connectors above, this one is configured entirely from the Spicepod: it takes a `from: cdc:<name>` source, and because nothing is there to peek at until a plugin posts, it cannot infer a schema. Declare `columns` and their types, alongside the `refresh_mode: changes` acceleration:

```yaml
datasets:
  - from: cdc:orders
    name: orders
    columns:
      - name: id
        type: bigint
      - name: total
        type: double precision
    acceleration:
      engine: cayenne
      mode: file
      refresh_mode: changes
      primary_key: id
      on_conflict:
        id: upsert
```

Without the declared schema the dataset fails to register rather than waiting for a first event.

A post to `/v1/datasets/{name}/cdc` blocks until the batch has been applied, so a Debezium sink commits its source offset on the response. Give the acceleration a durable `mode` for that acknowledgement to be worth anything: on the default `mode: memory` the applied rows are gone after a restart while the producer resumes from the offset it already committed, and unlike a connector-backed dataset there is no source for Spice to re-read to close the gap.

### Configuration

A replicated dataset needs a `primary_key` so that updates and deletes can be matched to existing rows, and an `on_conflict` rule so that repeated keys upsert rather than duplicate.

```yaml
datasets:
  - from: postgres:public.orders
    name: orders
    params:
      pg_host: postgres.example-org.com
      pg_port: '5432'
      pg_user: spice
      pg_pass: ${secrets:pg_pass}
      pg_db: myapp
      pg_sslmode: verify-full
    acceleration:
      enabled: true
      engine: cayenne
      mode: file
      refresh_mode: changes
      primary_key: id
      on_conflict:
        id: upsert
```

On startup Spice loads an initial snapshot of the table, then switches to streaming changes. No `refresh_check_interval` is required — changes are applied as they arrive rather than on a poll.

Any accelerator engine can back a replicated dataset. [Cayenne](../building-blocks/data-accelerators/cayenne.md) is built for this workload, sustaining a high-throughput change feed while serving analytical scans from the same table. This applies to datasets; catalog-level replication below is Cayenne-only.

### PostgreSQL prerequisites

Logical replication must be enabled on the source server:

```
wal_level = logical
max_replication_slots = 10
max_wal_senders = 10
```

Each replicated table needs a primary key, or `REPLICA IDENTITY FULL`, so that updates and deletes carry enough information to identify the affected row:

```sql
ALTER TABLE public.orders REPLICA IDENTITY FULL;
```

The connecting role needs the `REPLICATION` attribute, plus `SELECT` on the replicated tables. Spice creates and manages its own replication slot and publication.

{% hint style="warning" %}
An inactive replication slot causes the source server to retain write-ahead log segments indefinitely, which can exhaust disk on the primary. Drop the slot on the source if a replicated dataset is removed permanently.
{% endhint %}

### MySQL prerequisites

Row-based binary logging must be enabled on the source server, with complete row images:

```
log_bin = ON
binlog_format = ROW
binlog_row_image = FULL
binlog_row_value_options = ''
```

`binlog_row_image = FULL` is required because Spice's binlog decoder needs every row image to carry every column, and fails to decode one that omits any; rows themselves are matched by the configured `primary_key`, not by comparing whole images. `binlog_row_value_options` must be empty for the same reason: its only other setting, `PARTIAL_JSON`, logs JSON columns as partial diffs rather than whole values, and Spice rejects it at startup. A server can satisfy the first three settings and still fail on this one. The connecting user needs the replication privileges plus read access to the replicated tables:

```sql
-- Replication privileges are server-wide; MySQL has no narrower scope for them.
GRANT REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'spice'@'%';
-- Read access only needs to reach the tables being replicated. Repeat per table.
GRANT SELECT ON `app`.`orders` TO 'spice'@'%';
```

Spice validates all of this before it starts replicating and fails with a specific error naming what is missing.

Retain binary logs for at least as long as the replica may be offline. Spice resumes from the position it persisted, and a source that has purged past that position cannot be resumed from — the stream stops with an error naming the position that is gone. Size `binlog_expire_logs_seconds` against expected downtime. Where rebuilding is preferable to stopping, set `mysql_replication_invalid_checkpoint_behavior: restart` to drop the saved position and re-snapshot the table instead; the default is to error so that re-snapshotting a large source stays a deliberate choice.

Each replica registers a `server_id` on the source, which must be unique among every replica attached to that source. It is derived from the dataset name by default; set `mysql_replication_server_id` when a fixed value is needed.

Where the source server has [GTIDs](https://dev.mysql.com/doc/refman/8.4/en/replication-gtids.html) enabled, Spice tracks its replication position as a GTID set rather than a file-and-offset. A GTID set stays valid across a replica promotion, so replication survives a failover to a new primary without rebuilding the accelerated dataset.

### Catalog-level replication

A whole PostgreSQL database can be replicated from one catalog entry, with no per-table configuration. Set `refresh_mode: changes` on the catalog's acceleration and Spice replicates every table the `include` patterns match:

```yaml
catalogs:
  - from: pg
    name: pg
    include:
      - 'public.*'
    params:
      pg_host: postgres.example-org.com
      pg_db: myapp
      pg_user: spice
      pg_pass: ${secrets:pg_pass}
    acceleration:
      refresh_mode: changes
      mode: file
```

Every table shares one replication slot and one publication, so the write-ahead log is decoded once for the catalog rather than once per table. The catalog's `acceleration.mode` and `acceleration.params` apply uniformly to each table, and tables are reachable as `{catalog}.{schema}.{table}`.

A catalog's `acceleration.engine` accepts only `cayenne`, and its `refresh_mode` only `changes`; any other value is rejected when the Spicepod is read. The engine may be left out, as above, since `cayenne` is the default.

A table is eligible when its `REPLICA IDENTITY` yields a key Spice can upsert on, which is narrower than simply having a primary key:

| `REPLICA IDENTITY` | Eligible |
| ------------------ | -------- |
| `DEFAULT`          | With a primary key. |
| `FULL`             | With a primary key — `FULL` alone logs the whole row but names no upsert key. |
| `USING INDEX`      | When the nominated index is unique, non-partial, and on `NOT NULL` columns. |
| `NOTHING`          | Never, even with a primary key — no row identity is logged, so updates and deletes cannot be replicated. |

A table that qualifies under none of these is skipped with a warning naming its fix, and left out of the catalog namespace rather than failing the whole catalog. Narrow `include` and `exclude` to keep known-ineligible tables out, and reach them through federation or a `refresh_mode: full` dataset instead.

{% hint style="warning" %}
Catalog CDC acceleration is **Alpha**: its configuration may still change. Use a durable acceleration `mode` — the default `mode: memory` re-runs the initial snapshot of every table on each restart. Dropping and recreating a matched source table is not yet handled: the accelerated table can go on serving rows captured from the table it replaced, with no error and no warning ([#12110](https://github.com/spiceai/spiceai/issues/12110)). A rename is the same class of event. The stale mapping is held for the lifetime of the process, so restart the runtime after a migration that recreates or renames a replicated table.
{% endhint %}

### Handling deletes

CDC propagates hard deletes, which sets replication apart from incremental ingestion. A `DELETE` on the source removes the row from the replica on the next change event, with no reconciling full refresh and no soft-delete convention in the source schema.

Sources that expose no change feed at all — HTTP APIs, for example — use [incremental ingestion](data-acceleration/README.md#incremental-ingestion) with `refresh_mode: append` instead, where deletes are handled by soft-delete tombstones or a periodic full refresh.

### Related

* [Analytics Replica](../use-cases/analytics-replica.md) — the deployment pattern built on replication
* [Data Acceleration](data-acceleration/README.md) — refresh modes, incremental ingestion, and retention
* [Database CDN](../use-cases/database-cdn.md) — colocating a hot working set with an application
* [Change data capture](https://spiceai.org/docs/features/cdc) in the Spice.ai OSS documentation
* [MySQL CDC recipe](https://github.com/spiceai/cookbook/tree/trunk/mysql/cdc) — binlog replication from a MySQL table
* [Amazon Aurora MySQL CDC recipe](https://github.com/spiceai/cookbook/tree/trunk/mysql/rds-aurora-cdc) — the same setup against an Aurora MySQL cluster
* [PostgreSQL catalog CDC recipe](https://github.com/spiceai/cookbook/tree/trunk/catalogs/postgres-cdc) — replicating every table of a database from one catalog entry
