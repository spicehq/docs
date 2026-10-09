---
icon: plug
description: Connect your databases, warehouses, lakes, and APIs to Spice.ai Cloud.
---

# Connecting Data

## Overview

Spice.ai supports [federated SQL queries](../features/federated-sql-query.md) across a wide range of data sources. Data connectors let you query external data directly or replicate it into Spice for accelerated access.

For the full list of connectors and configuration options, see [Data Connectors](../building-blocks/data-connectors/).

## Supported Data Sources

| Category                 | Connectors                                                                                                                                                                                                                               |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Relational databases** | [PostgreSQL](../building-blocks/data-connectors/postgres.md), [MySQL](../building-blocks/data-connectors/mysql.md), [MSSQL](../building-blocks/data-connectors/mssql.md), [ClickHouse](../building-blocks/data-connectors/clickhouse.md) |
| **Data warehouses**      | [Snowflake](../building-blocks/data-connectors/snowflake.md), [Databricks](../building-blocks/data-connectors/databricks.md), [Dremio](../building-blocks/data-connectors/dremio.md)                                                     |
| **Data lakes & formats** | [S3](../building-blocks/data-connectors/s3.md), [Delta Lake](../building-blocks/data-connectors/delta-lake.md), [ABFS](../building-blocks/data-connectors/abfs.md)                                                                       |
| **APIs & other**         | [GraphQL](../building-blocks/data-connectors/graphql.md), [GitHub](../building-blocks/data-connectors/github.md), [SharePoint](../building-blocks/data-connectors/sharepoint.md)                                                         |
| **Streaming**            | [Debezium CDC](../building-blocks/data-connectors/debezium.md)                                                                                                                                                                           |
| **Embedded / local**     | [DuckDB](../building-blocks/data-connectors/duckdb.md), [SQLite](../building-blocks/data-connectors/ftp.md)                                                                                                                              |

## Adding a Data Source

### 1. Store credentials as secrets

Data source credentials should be stored as [secrets](../cloud/portal/apps/secrets.md) in your project. In the Portal, go to your project → **Secrets** and create entries for connection strings, access keys, or tokens.

{% hint style="info" %}
Secret values are write-only and cannot be retrieved after saving.
{% endhint %}

### 2. Configure the dataset in your Spicepod

Add a dataset entry to your project's `spicepod.yaml` referencing the connector and secrets:

```yaml
datasets:
  - from: postgres:my_schema.my_table
    name: my_table
    params:
      pg_host: my-database.example.com
      pg_port: "5432"
      pg_db: my_database
      pg_user: "${secrets:PG_USER}"
      pg_pass: "${secrets:PG_PASS}"
```

### 3. Deploy

Commit and deploy your project to make the dataset available for querying.

## Data Acceleration

For faster queries, enable [data acceleration](../features/data-acceleration/) to cache datasets in a local engine:

```yaml
datasets:
  - from: postgres:my_schema.my_table
    name: my_table
    acceleration:
      engine: arrow  # or duckdb, sqlite, postgres
      refresh_interval: 10m
```

Available acceleration engines:
- [Arrow](../features/data-acceleration/in-memory-arrow-data-accelerator.md) — In-memory, fastest performance.
- [DuckDB](../features/data-acceleration/duckdb-data-accelerator.md) — Embedded analytical engine.
- [SQLite](../features/data-acceleration/sqlite-data-accelerator.md) — Lightweight, persistent.
- [PostgreSQL](../features/data-acceleration/postgresql-data-accelerator.md) — Full-featured relational engine.

## Common Issues

### Table not found

Spice normalizes unquoted SQL identifiers to **lowercase** (PostgreSQL-style). If your source uses mixed-case table or column names, double-quote them:

```sql
SELECT "ColumnName" FROM "MySchema"."MyTable"
```

### Object store file format errors

When connecting to object stores (S3, ABFS):
- **Folder paths** require an explicit `file_format` (e.g., `parquet`, `csv`).
- **Single file paths** auto-detect the format from the file extension.

### Schema changes break data refresh

Spice infers the schema at deployment time. If the source schema changes, refresh will fail. **Redeploy the project** to pick up the new schema.

### Connection failures

- Confirm the data source is network-accessible from Spice.ai Cloud (firewalls, IP allowlists).
- Verify secrets are correct and referenced with `${secrets:SECRET_NAME}` syntax.
- Check connector-specific docs for required parameters.

## Further Reading

- [Data Connectors reference](../building-blocks/data-connectors/)
- [Data Acceleration](../features/data-acceleration/)
- [Federated SQL Query](../features/federated-sql-query.md)
- [Catalogs](../building-blocks/catalogs/index.md)
