---
description: Attach a columnar analytics replica to an operational database without ETL or migration
icon: database
---

# Analytics Replica

The **analytics replica pattern** attaches a dedicated columnar node to an operational database and lets it absorb the analytical query load. The primary keeps serving transactions. Analytical questions — which customers churned today, which orders are stuck, how revenue moved this hour — run against a replica that stays seconds behind.

It is the shortest path from an operational database to analytical and AI workloads, because nothing has to be migrated and no pipeline has to be built.

### The problem

Analytical queries and transactional queries want opposite things from a database. Transactions want narrow row lookups and short-lived locks. Analytics wants wide scans over large ranges. Running both on one primary means the reporting query and the checkout path compete for the same buffer pool and CPU.

The established workarounds each trade one problem for another:

| Approach | What it costs |
| -------- | ------------- |
| **ETL pipeline** | Data arrives minutes or hours late, and the pipeline itself becomes infrastructure to build, schedule, monitor, and repair |
| **Read replica** | Removes load from the primary, but runs the same row-oriented engine — analytical scans are just as slow |
| **HTAP database** | Requires migrating off the current database, and recouples transactional and analytical failure domains |
| **Data warehouse** | Strong for analytics, but reached through a pipeline, so it inherits the latency and the operational burden |

### How it works

An analytics replica connects to the operational database, loads an initial snapshot, then uses change data capture (CDC) to apply committed changes continuously from the source's native changelog — the PostgreSQL write-ahead log, a MongoDB change stream, a DynamoDB stream. There is no batch interval; a committed change is queryable within seconds.

The replica stores data in a columnar format on its own storage and compute, so scans are fast and the load never reaches the primary. Queries run through the same federated SQL interface as the rest of the platform, which means a replicated table can be joined against a data lake, a warehouse, or another operational system in a single query.

```yaml
datasets:
  - from: postgres:public.orders
    name: orders
    params:
      pg_host: postgres.example-org.com
      pg_user: spice
      pg_pass: ${secrets:pg_pass}
      pg_db: myapp
    acceleration:
      enabled: true
      engine: cayenne
      mode: file
      refresh_mode: changes
      primary_key: id
      on_conflict:
        id: upsert
```

See [Database Replication and CDC](../features/database-replication-and-cdc.md) for the supported sources, source prerequisites, and full configuration reference.

### Why it differs from a read replica

A read replica and an analytics replica solve different halves of the problem:

* A **read replica** moves load off the primary but keeps the row-oriented storage and execution engine, so a query scanning millions of rows is no faster than it was.
* An **analytics replica** changes the storage layout and the execution engine. Data is columnar, scans read only the columns a query touches, and segment statistics skip data that cannot match.

Both keep the primary healthy. Only one makes the analytical query fast.

### Incremental adoption

The pattern is adopted table by table. Replicate one table, point one dashboard or one agent at it, and leave everything else untouched. There is no cutover, no schema migration, and no change to how the application writes.

That property matters most when the destination is an AI agent. Grounding an agent in production data usually stalls on the pipeline needed to expose that data safely. Replicating the handful of tables the agent needs is a smaller commitment than building a warehouse feed, and the agent queries data that is seconds old rather than a day stale.

### Governing access

Because queries reach the replica rather than the operational database, access is governed at the replica:

* Agents and applications authenticate to Spice instead of holding database credentials, so the primary's credentials never leave the infrastructure that owns them.
* [Policy](../enterprise/features/policy.md) rules apply row-level filters and column masking at query time, based on the identity of the caller.
* Queries are recorded, so what an agent read is auditable after the fact.

### Related

* [Database Replication and CDC](../features/database-replication-and-cdc.md) — configuration, supported sources, and prerequisites
* [Federated SQL Query](../features/federated-sql-query.md) — joining a replica against other systems
* [Database CDN](database-cdn.md) — colocating a hot working set with an application
* [Agentic AI Apps](agentic-ai-apps.md) — grounding agents in operational data
