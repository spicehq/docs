---
description: Frequently asked questions
icon: circle-question
---

# FAQ

### What's the difference between Spice.ai OSS, Cloud, and Enterprise?

- **Spice.ai OSS** — the open-source Spice runtime. Self-hosted, free, Apache 2.0.
- **Spice.ai Cloud** — a managed, multi-tenant hosted service running Spice as a platform with additional building blocks (cloud data warehouse, model training/inference, AI gateway).
- **Spice.ai Enterprise** — a self-hosted enterprise distribution of Spice with advanced features (HA, RBAC, SSO, governance, premium connectors), enterprise support, and an SLA.

See [Distributions](https://docs.spice.ai/docs/enterprise/getting-started/distributions) for a detailed comparison.

### What's the difference between the Spice.ai Cloud Platform and Spice.ai OSS?

[**Spice.ai OSS**](https://github.com/spiceai/spiceai) is an open-source project created by the Spice AI team that provides a unified SQL query interface to locally materialize, accelerate, and query data tables sourced from any database, data warehouse, or data lake.

<figure><img src="../.gitbook/assets/image (41).png" alt=""><figcaption><p>The Spice.ai OSS runtime</p></figcaption></figure>

**The Spice.ai Cloud Platform** is a data and AI application platform that provides a set of building-blocks to create AI and agentic applications. Building blocks include a cloud-data-warehouse, ML model training and inference, and a cloud-scale, managed Spice.ai OSS cloud-hosted service.

<figure><img src="../.gitbook/assets/OGP (1).png" alt=""><figcaption><p>The Spice.ai Cloud Platform</p></figcaption></figure>

### How much does Spice.ai Cloud cost?

It's free to [get an API key](https://spice.ai) to use the [Community Edition](/broken/pages/TswKQuxvMpeSWfvqFhxa).

Customers who need resource limits, service-level guarantees, or priority support we offer [high-value paid tiers](cloud/pricing/plans.md) based on usage.

### What level of support do you offer?

We offer enterprise-grade support with an SLA [for Enterprise Plans](cloud/pricing/plans.md).

For standard plans we offer [best-effort community support](https://github.com/spicehq/cloud-docs/blob/trunk/broken-reference/README.md) in Discord.

### What's your approach to security and compliance?

See [Security](../security/security.md). The Spice.ai Cloud Platform is SOC 2 Type II compliant.

### What SQL query engine/dialect do you support?

Spice.ai OSS is built on [Apache DataFusion](https://datafusion.apache.org/) as its primary query execution engine, providing vectorized, multi-threaded query processing. It uses the PostgreSQL SQL dialect. Spice also supports [DuckDB](https://duckdb.org/), SQLite, and PostgreSQL as acceleration engines at the dataset level.

### What AI capabilities does Spice provide?

Spice provides unified APIs for data and AI workflows, including model inference, embeddings, and an [AI gateway](features/ai-gateway.md) supporting OpenAI, Anthropic, Amazon Bedrock, and xAI. Spice also includes advanced tools such as [vector and hybrid search](features/search-and-retrieval.md), text-to-SQL, and data sampling.

### What AI model providers does Spice support?

Spice supports local model serving (e.g. Llama) and gateways to hosted AI platforms including OpenAI, Anthropic, xAI, and Amazon Bedrock. See [Model Providers](building-blocks/model-providers/) for details.

### Can Spice handle federated queries?

Yes. Spice natively supports [federated SQL queries](features/federated-sql-query.md) across disparate data sources with advanced query push-down capabilities, executing portions of queries directly on source databases to reduce data transfer and improve performance.

### Can Spice integrate with existing BI tools?

Yes. Spice integrates with BI tools through standard SQL interfaces (ODBC, JDBC, ADBC, Arrow Flight SQL), enabling accelerated, real-time analytics for dashboards and reporting.

### Does Spice support Change Data Capture (CDC)?

Yes. Spice supports streaming ingestion from several sources:

- **Native PostgreSQL logical replication** (recommended for PostgreSQL sources). Spice connects directly to the source using Postgres' `wal_level=logical` and streams `INSERT`/`UPDATE`/`DELETE` events into the accelerator. See [PostgreSQL Logical Replication](https://spiceai.org/docs/features/cdc/postgres-replication) in the OSS documentation.
- **[DynamoDB Streams](building-blocks/data-connectors/dynamodb.md)** for Amazon DynamoDB sources — Spice consumes the table's change stream and applies `INSERT`/`UPDATE`/`DELETE` events to the accelerator with `refresh_mode: changes`.
- **[Apache Kafka](building-blocks/data-connectors/kafka.md)** for event-streaming topics — Spice consumes records directly with `refresh_mode: append` for real-time, append-only acceleration.
- **[Debezium](building-blocks/data-connectors/debezium.md)** (over Kafka) for sources where Debezium is already deployed, or for databases without a native Spice CDC path (MySQL, SQL Server, etc.).

### How do I keep an accelerated dataset incrementally up-to-date?

For sources with a monotonically-increasing version column (e.g. `updated_at`), Spice incrementally ingests new and modified records using `time_column` + `refresh_mode: append`, with `refresh_append_overlap` to tolerate clock skew and `retention_period` to evict old or soft-deleted records. See [Incremental Ingestion](features/data-acceleration/README.md#incremental-ingestion) for configuration details and examples.

### Does Spice support schema evolution?

Spice infers the schema for datasets and views at startup. How the runtime responds to a source schema change while running (e.g. columns added, removed, or retyped) is controlled per dataset by the `on_schema_change` setting.

By default (`on_schema_change: block`), Spice does not apply source schema changes automatically: the dataset stays healthy and continues serving queries using the schema registered at startup. To evolve the schema without restarting, set `on_schema_change` to one of:

- `append_new_columns` — add newly-appeared source columns; reject removals and incompatible changes.
- `sync_all_columns` — keep the registered schema synchronized with the source schema.
- `drop_and_recreate` — apply widening changes in place; otherwise drop and recreate the accelerated table (destructive, and only with `refresh_mode: full`).
- `fail` — fail the refresh when the source schema diverges.

Restarting the runtime also re-infers the schema from the source and re-initializes the accelerated table.

### What is Data-grounded AI?

Data-grounded AI anchors models in accurate, current, domain-specific data rather than relying solely on pre-trained knowledge. Spice unifies enterprise data across databases, data lakes, and APIs, dynamically incorporating real-world context at inference time. This helps minimize hallucinations, reduce operational risk, and build trust in AI by delivering reliable, relevant outputs.

### Where can I find examples and recipes?

The [Spice.ai Cookbook](https://github.com/spiceai/cookbook) provides quickstarts and examples demonstrating Spice capabilities, including federated queries, RAG, text-to-SQL, and more.
