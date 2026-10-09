---
description: Federated SQL Query documentation
icon: arrow-progress
---

# Federated SQL Query

<figure><img src="../.gitbook/assets/Spice.ai Federation and Acceleration .png" alt=""><figcaption></figcaption></figure>

Spice supports federated queries, enabling you to join and combine data from multiple sources, including databases (PostgreSQL, MySQL), data warehouses (Databricks, Snowflake, BigQuery), and data lakes (S3, MinIO). For a full list of supported sources, see [Data Connectors](https://docs.spiceai.org/components/data-connectors).

## SQL Query

### Playground SQL Explorer

The Playground SQL Explorer is the fastest way to get started with federated queries, debugging queries, and iterating quickly. The SQL Query Editor can be accessed by clicking on the **SQL Explorer** tab after selecting **Playground** in the project navigation bar.

<figure><img src="../.gitbook/assets/CleanShot 2026-01-09 at 00.43.27@2x.png" alt=""><figcaption><p>The Playground SQL Query Editor.</p></figcaption></figure>

See [sql-query-editor.md](../cloud/portal/playground/sql-query-editor.md "mention") for further documentation on using the SQL Query Editor.

### Apache Arrow Flight API

For production applications, leveraging the high-performance [Apache Arrow Flight](../cloud/api/sql-query/apache-arrow-flight-api.md) endpoint is recommended. The Spice SDKs always query using Arrow Flight.

See [apache-arrow-flight-api.md](../cloud/api/sql-query/apache-arrow-flight-api.md "mention") for further documentation on using Apache Arrow Flight APIs.

### HTTP API

SQL Query is also accessible via a standard HTTP API.

See [http-api.md](../cloud/api/sql-query/http-api.md "mention") for further documentation on using the HTTP SQL API.
