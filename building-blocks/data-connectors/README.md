---
description: Learn how to use Data Connector to query external data.
icon: database
---

# Data Connectors

Data Connectors provide connections to databases, data warehouses, and data lakes for federated SQL queries and data replication.

Supported Data Connectors include:

| Name                               | Description          | Protocol/Format              |
| ---------------------------------- | -------------------- | ---------------------------- |
| `databricks (mode: delta_lake)`    | Databricks           | S3/Delta Lake                |
| `delta_lake`                       | Delta Lake           | Delta Lake                   |
| `dremio`                           | Dremio               | Arrow Flight                 |
| `duckdb`                           | DuckDB               | Embedded                     |
| `github`                           | GitHub               | GitHub API                   |
| `postgres`                         | PostgreSQL           |                              |
| `s3`                               | S3                   | Parquet, CSV                 |
| `mysql`                            | MySQL                |                              |
| `delta_lake`                       | Delta Lake           | Delta Lake                   |
| `graphql`                          | GraphQL              | JSON                         |
| `databricks (mode: spark_connect)` | Databricks           | Spark Connect                |
| `flightsql`                        | FlightSQL            | Arrow Flight SQL             |
| `mssql`                            | Microsoft SQL Server | Tabular Data Stream (TDS)    |
| `snowflake`                        | Snowflake            | Arrow                        |
| `spark`                            | Spark                | Spark Connect                |
| `spice.ai`                         | Spice.ai             | Arrow Flight                 |
| `iceberg`                          | Apache Iceberg       | Parquet                      |
| `abfs`                             | Azure BlobFS         | Parquet, CSV                 |
| `clickhouse`                       | Clickhouse           |                              |
| `debezium`                         | Debezium CDC         | Kafka + JSON                 |
| `dynamodb`                         | DynamoDB             |                              |
| `ftp`, `sftp`                      | FTP/SFTP             | Parquet, CSV                 |
| `http`, `https`                    | HTTP(s)              | Parquet, CSV                 |
| `sharepoint`                       | Microsoft SharePoint | Unstructured UTF-8 documents |

## Object Store File Formats

For data connectors that are object store compatible, if a folder is provided, the file format must be specified with `params.file_format`.

If a file is provided, the file format will be inferred, and `params.file_format` is unnecessary.

File formats currently supported are:

| Name                                           | Parameter              | Supported | Is Document Format |
| ---------------------------------------------- | ---------------------- | --------- | ------------------ |
| [Apache Parquet](https://parquet.apache.org/)  | `file_format: parquet` | ✅         | ❌                  |
| [CSV](../../cloud/reference/file-format.md#csv) | `file_format: csv`     | ✅         | ❌                  |
| [Apache Iceberg](https://iceberg.apache.org/)  | `file_format: iceberg` | Roadmap   | ❌                  |
| JSON                                           | `file_format: json`    | Roadmap   | ❌                  |
| Microsoft Excel                                | `file_format: xlsx`    | Roadmap   | ❌                  |
| Markdown                                       | `file_format: md`      | ✅         | ✅                  |
| Text                                           | `file_format: txt`     | ✅         | ✅                  |
| PDF                                            | `file_format: pdf`     | Alpha     | ✅                  |
| Microsoft Word                                 | `file_format: docx`    | Alpha     | ✅                  |

File formats support additional parameters in the `params` (like `csv_has_header`) described in [File Formats](../../cloud/reference/file-format.md)

If a format is a document format, each file will be treated as a document, as per [document support](./#document-support) below.

{% hint style="info" %}
**Note** Document formats in Alpha (e.g. pdf, docx) may not parse all structure or text from the underlying documents correctly.
{% endhint %}

## Identifier Case Sensitivity and Quoting

Spice follows [PostgreSQL conventions](https://spiceai.org/docs/reference/sql) for identifier handling: **unquoted identifiers are normalized to lowercase**. This applies to both the `from` field in dataset definitions and the `name` field used for SQL queries.

### Quoting in the `from` field

To reference a table or schema with mixed-case or uppercase characters in the `from` field, wrap each case-sensitive part in double quotes:

```yaml
datasets:
  # Without quoting — "ActionExecutions" is lowercased to "actionexecutions"
  - from: postgres:my_schema.ActionExecutions
    name: action_executions

  # With quoting — case is preserved for the table name
  - from: postgres:my_schema."ActionExecutions"
    name: action_executions

  # Quote each part individually as needed
  - from: postgres:"MySchema"."ActionExecutions"
    name: action_executions
```

Each dotted part of the identifier is treated independently — quote only the parts that require case preservation. For example, `postgres:my_schema."ActionExecutions"` preserves the case of `ActionExecutions` while `my_schema` is normalized to lowercase.

This applies to all federated database connectors where the `from` field references a table identifier (e.g. `postgres`, `mysql`, `snowflake`, `databricks`, `clickhouse`, `mssql`, `duckdb`, `dremio`, `flightsql`, `spark`, `mongodb`, `oracle`). Connectors that interpret `from` as a file path (e.g. `s3`, `delta_lake`, `ftp`, `abfs`) do not apply identifier normalization.

### Quoting in the `name` field

The `name` field controls the table name used in Spice SQL queries and follows the same lowercase normalization. To preserve case in the dataset name, wrap the value in double quotes. In YAML, use single quotes around the double-quoted value:

```yaml
datasets:
  - from: postgres:my_schema."ActionExecutions"
    name: '"ActionExecutions"'
```

```sql
-- Query using the preserved-case name
SELECT * FROM "ActionExecutions";
```

If you don't need to preserve case in queries, a lowercase `name` works without quoting:

```yaml
datasets:
  - from: postgres:my_schema."ActionExecutions"
    name: action_executions
```

```sql
SELECT * FROM action_executions;
```

Dataset `name` quoting works regardless of connector type.
