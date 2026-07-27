---
icon: plug
description: Connect your databases, warehouses, lakes, and APIs to Spice.ai Cloud.
---

# Connecting Data

## Overview

Spice.ai supports [federated SQL queries](https://docs.spice.ai/features/federated-sql-query) across a wide range of data sources. Data connectors let you query external data directly or replicate it into Spice for accelerated access.

For the full list of connectors and configuration options, see [Data Connectors](https://docs.spice.ai/building-blocks/data-connectors/).

## Supported Data Sources

| Category                 | Connectors                                                                                                                                                                                                                               |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Relational databases** | [PostgreSQL](https://docs.spice.ai/building-blocks/data-connectors/postgres), [MySQL](https://docs.spice.ai/building-blocks/data-connectors/mysql), [MSSQL](https://docs.spice.ai/building-blocks/data-connectors/mssql), [ClickHouse](https://docs.spice.ai/building-blocks/data-connectors/clickhouse) |
| **Data warehouses**      | [Snowflake](https://docs.spice.ai/building-blocks/data-connectors/snowflake), [Databricks](https://docs.spice.ai/building-blocks/data-connectors/databricks), [Dremio](https://docs.spice.ai/building-blocks/data-connectors/dremio)                                                     |
| **Data lakes & formats** | [S3](https://docs.spice.ai/building-blocks/data-connectors/s3), [Delta Lake](https://docs.spice.ai/building-blocks/data-connectors/delta-lake), [ABFS](https://docs.spice.ai/building-blocks/data-connectors/abfs)                                                                       |
| **APIs & other**         | [GraphQL](https://docs.spice.ai/building-blocks/data-connectors/graphql), [GitHub](https://docs.spice.ai/building-blocks/data-connectors/github), [SharePoint](https://docs.spice.ai/building-blocks/data-connectors/sharepoint)                                                         |
| **Streaming**            | [Debezium CDC](https://docs.spice.ai/building-blocks/data-connectors/debezium)                                                                                                                                                                           |
| **Embedded / local**     | [DuckDB](https://docs.spice.ai/building-blocks/data-connectors/duckdb), [SQLite](https://docs.spice.ai/features/data-acceleration/sqlite-data-accelerator)                                                                                                              |

## Adding a Data Source

### 1. Store credentials as secrets

Data source credentials should be stored as [secrets](https://docs.spice.ai/docs/portal/apps/secrets) in your app. In the Portal, go to your app → **Secrets** and create entries for connection strings, access keys, or tokens.

{% hint style="info" %}
Secret values are write-only and cannot be retrieved after saving.
{% endhint %}

### 2. Configure the dataset in your Spicepod

Add a dataset entry to your app's `spicepod.yaml` referencing the connector and secrets:

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

Commit and deploy your app to make the dataset available for querying.

## Data Acceleration

For faster queries, enable [data acceleration](https://docs.spice.ai/features/data-acceleration/) to cache datasets in a local engine:

```yaml
datasets:
  - from: postgres:my_schema.my_table
    name: my_table
    acceleration:
      engine: arrow  # or duckdb, sqlite, postgres
      refresh_interval: 10m
```

Available acceleration engines:
- [Arrow](https://docs.spice.ai/features/data-acceleration/in-memory-arrow-data-accelerator) — In-memory, fastest performance.
- [DuckDB](https://docs.spice.ai/features/data-acceleration/duckdb-data-accelerator) — Embedded analytical engine.
- [SQLite](https://docs.spice.ai/features/data-acceleration/sqlite-data-accelerator) — Lightweight, persistent.
- [PostgreSQL](https://docs.spice.ai/features/data-acceleration/postgresql-data-accelerator) — Full-featured relational engine.

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

Spice infers the schema at deployment time. If the source schema changes, refresh will fail. **Redeploy the app** to pick up the new schema.

### Connection failures

- Confirm the data source is network-accessible from Spice.ai Cloud (firewalls, IP allowlists).
- Verify secrets are correct and referenced with `${secrets:SECRET_NAME}` syntax.
- Check connector-specific docs for required parameters.

## Further Reading

- [Data Connectors reference](https://docs.spice.ai/building-blocks/data-connectors/)
- [Data Acceleration](https://docs.spice.ai/features/data-acceleration/)
- [Federated SQL Query](https://docs.spice.ai/features/federated-sql-query)
- [Data Connectors](https://docs.spice.ai/building-blocks/data-connectors/)
