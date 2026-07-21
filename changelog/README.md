---
description: New updates and improvements to the Spice.ai Cloud Platform
icon: clock-rotate-left
---

# Changelog

{% updates format="full" %}
{% update date="2026-06-30" %}
## June 2026

### Highlights

* **Spice v2 Upgrade** – The portal now guides apps on stable v1.x to Spice v2 with an in-app upgrade banner and upgrade guide.
* **Dedicated Clusters** – Organizations can now run apps on dedicated private clusters with tenant isolation and private connectivity via AWS PrivateLink.
* **Cluster Management** – The new Clusters page lists an organization's private clusters, and deployments can target specific cluster node groups.
* **Public App Search** – Added full-text search across public apps on Spice Rack.
* **Simplified App Endpoints** – Each app now exposes a single data-plane endpoint, with a private endpoint for apps on private clusters.
* **Searchable GitHub Branches** – Branch selection in GitHub connector settings is now searchable.
* **Deployment Log Level** – The runtime log level is now configurable per deployment.

### Runtime

**Stable channel**

Spice runtime [v2.0](https://spiceai.org/releases/v2.0-stable) (Jun 5, 2026) — a major release, now generally available:

* **Spice Cayenne (GA)** – The Cayenne data accelerator is generally available on the Vortex compressed columnar format, with a high-throughput CDC write path, `MERGE INTO`, SQL-defined partitioning, and a dedicated compaction runtime.
* **Distributed Query (GA)** – Multi-active, highly-available distributed query built on Apache Ballista, with object-store-native clustering, distributed ingestion, data-local routing, and async queries via `/v1/queries`.
* **Enterprise Security** – Mutual TLS across endpoints and connectors, OIDC bearer-token verification, Cedar-based authorization with row- and column-level filtering, and HashiCorp Vault and Azure Key Vault secret stores.
* **CDC and Write-Back** – Native CDC from MongoDB Change Streams and PostgreSQL WAL, durable Kafka CDC offsets, DML write-back for PostgreSQL, Snowflake, DynamoDB, and Arrow, and DDL with `MERGE INTO` for Iceberg catalogs.
* **Rust CLI and DataFusion v52** – The `spice` CLI is rewritten in Rust as a single self-contained binary with an async query REPL, and the engine moves to DataFusion v52.
* v2.0 includes breaking changes — review the [release notes](https://spiceai.org/releases/v2.0-stable) before upgrading.

Spice runtime [v2.0.1](https://spiceai.org/releases/v2.0.1) (Jun 17, 2026):

* Improved Apache Iceberg read performance with parallel file scanning.
* Fixed S3 refresh-skip so unchanged objects are not re-downloaded, added startup retries for source files that are not yet available, and fixed path-style addressing for dotted bucket names.
* Fixed acceleration refresh endpoints and distributed-cluster accelerated serving to work for all accelerated datasets.
* Fixed authenticated queries to consistently run as the requesting user, correctly scoping per-user results caching.

### SDKs

* **spice-rs 3.2.0** – Added mTLS client certificate support, query bindings, and dataset refresh; Flight connections are now lazy for HTTP-only clients, and Arrow upgraded to v58.

<details>

<summary>Bug Fixes</summary>

* Fixed hosted AI secret linking so newly created apps correctly use the organization's OpenAI secret.
* Improved app deletion reliability with automatic retries and correct resource cleanup ordering.
* Fixed async SQL mode detection for clustered deployments.
* Fixed spicepod validation to prevent unintended payload overwrites on deployment and unpause.

</details>

{% endupdate %}

{% update date="2026-05-31" %}
## May 2026

### Highlights

* **MCP Streamable HTTP** – Spice apps now support Model Context Protocol connections over Streamable HTTP.
* **App Pause and Resume** – Apps can be manually paused and resumed, with settings remaining accessible while paused.
* **Secondary API Keys** – Apps now issue a secondary API key, enabling zero-downtime key rotation.
* **OAuth Client Expiry and Scopes** – OAuth clients now support expiration and app-level scopes.
* **Chat and Responses API Improvements** – Improved the OpenAI-compatible `/v1/chat/completions` and `/v1/responses` endpoints.
* **Model Provider Catalog Updates** – Provider templates now include model retirement notices and current model lists.
* **Trace History** – Runtime-issued queries are now hidden from trace history by default for a cleaner query view.

### Runtime

**Stable channel**

Spice runtime [v1.11.6](https://spiceai.org/releases/v1.11.6) (May 6, 2026):

* Improved DynamoDB bootstrap ingestion throughput for `refresh_mode: changes` datasets on large tables.
* Added configurable liveness, readiness, and startup probes to the Helm chart.

**Preview channel**

Spice runtime [v2.0-rc.5](https://spiceai.org/releases/v2.0-rc.5) (May 27, 2026):

* Completed mutual TLS support with certificate hot-reload, optional or required client-certificate modes for HTTP and Flight, and outbound client certificates for FlightSQL and Spice.ai connections.
* Added MongoDB Change Streams as a native CDC source and durable Kafka CDC offset persistence.
* Expanded DML write-back with `INSERT`/`UPDATE`/`DELETE` for PostgreSQL and Snowflake, Arrow primary-key upserts, and DuckLake promoted to Beta.
* Improved Cayenne performance across CDC ingest, background compaction, scan caching, and parallel partition writes.
* Added SQL user-defined functions in spicepods, provider-aware LLM prompt caching, and Responses API support across all model providers.

Spice runtime [v2.0-rc.4](https://spiceai.org/releases/v2.0-rc.4) (Apr 30, 2026):

* Added an Elasticsearch data connector (Alpha) with hybrid search combining BM25 full-text, kNN vector search, and Reciprocal Rank Fusion.
* Added native PostgreSQL CDC via WAL logical replication, removing the need for Debezium or Kafka.
* Added multi-vector embeddings with MaxSim scoring and a `rerank` UDTF for late-interaction retrieval.
* Added HashiCorp Vault and Azure Key Vault secret stores.
* Added MCP Streamable HTTP transport, an Azure Cosmos DB data connector (RC), and promoted the Git connector to RC.

<details>

<summary>Bug Fixes</summary>

* Fixed GitHub app installation verification during GitHub connection setup.
* Fixed app search in the command menu.
* Fixed memory and connection stream leaks in query handling for improved platform reliability.
* Fixed enterprise image tags missing from the container images listing.

</details>

{% endupdate %}

{% update date="2026-04-30" %}
## April 2026

### Highlights

* **Text-to-SQL Playground** – Added a dedicated Text-to-SQL playground and NSQL API page, followed by improvements to loading behavior and query UX.
* **Update Channel for Preview Releases** – Spice Cloud users can now select Preview or Stable update channels, with Spice apps automatically upgraded to preview versions.
* **Org Limits API** – Added `GET /v1/limits` to return organization plan and resource limits.
* **Monitoring and Deployment Visibility** – Added cluster-aware grouping for SpicepodCluster monitoring and refined acceleration graph visibility in the portal.
* **Audit Logging** – Added broader audit logging for login, app, deployment, and admin operations.

### Runtime

**Stable channel**

Spice runtime [v1.11.5](https://spiceai.org/releases/v1.11.5) (Apr 1, 2026):

* Improved `on_zero_results: use_source` fallback performance by applying physical optimizer rules to fallback execution plans.
* Improved Delta Lake file pruning for `>=` timestamp predicates to reduce unnecessary scans.
* Added PostgreSQL partitioned table support for schema discovery and querying.
* Improved S3 Parquet read performance by coalescing reads into fewer requests.
* Fixed Cayenne target file size handling so written files better match configured size.

**Preview channel**

Spice runtime [v2.0-rc.3](https://spiceai.org/releases/v2.0-rc.3) (Apr 21, 2026):

* Added OAuth2 refresh-token authentication, query-parameter pagination, and map-to-array response conversion to the HTTP data connector.
* Improved Databricks and Unity Catalog reliability with resilience controls, permission prechecks, and structured error reporting.
* Improved Snowflake and ADBC dataset registration performance and observability.
* Added authorization header support to the OpenTelemetry metrics exporter and fixed exporter reliability issues.
* Expanded sort and limit pushdown across connectors, including Oracle and MSSQL, and added MongoDB SRV connection support.

Spice runtime [v2.0-rc.2](https://spiceai.org/releases/v2.0-rc.2) (Apr 10, 2026):

* Improved distributed Cayenne query and write routing with partition-aware execution and distributed DML handling.
* Added `MERGE INTO` support for Cayenne catalog tables with distributed execution safeguards.
* Added `PARTITION BY` support for Cayenne `CREATE TABLE` statements with persisted partition metadata.
* Added catalog connectors for PostgreSQL, MySQL, MSSQL, and Snowflake, and expanded schema and table discovery coverage.
* Added HTTP pagination support and expanded JSON ingestion support, including `json_pointer` extraction.

<details>

<summary>Bug Fixes</summary>

* Fixed deployment status inconsistencies in portal deployment views.
* Improved catalog explorer performance with lazy metadata loading and related rendering fixes.
* Fixed playground duration formatting, trace history filtering, and SQL error display behavior.
* Fixed monitoring overview staleness by disabling inappropriate caching on the monitoring page.
* Improved GitHub connector loading-state handling to separate organization loading from app installation state.

</details>

{% endupdate %}

{% update date="2026-03-31" %}
## March 2026

### Highlights

* **Cluster Metrics** – Added cluster executor metrics (`active_executors_count`) to monitoring endpoints.
* **Public Navigation Updates** – Added org and app selectors in topbar for public views.
* **Model Template Updates** – Updated Bedrock model templates to current Amazon Nova model IDs.

### Runtime

Spice runtime [v2.0-rc.1](https://spiceai.org/releases/v2.0-rc.1) (Mar 4, 2026) — first release candidate for v2.0:

* **Active-Active HA Distributed Query** – Object-store-native distributed query with dynamic cluster sizing, distributed ingestion for partitioned accelerated tables, and faster failure detection.
* **Spice Cayenne RC** – Staged append writes via WAL, file-based retention deletes, composite partitioning with `partition_by: [col1, col2]`, and distributed ingestion across executors.
* **DataFusion v52.2.0** – Sort pushdown, a new merge join, and dynamic filters.
* **DDL via SQL** – `CREATE TABLE` and `DROP TABLE` support for Iceberg and Cayenne catalogs.
* **DuckLake Catalog & Connector** – Lakehouse-style data management; new GCS data connector (Alpha); Rust CLI rewrite for a unified single-binary experience.

Spice runtime [v1.11.3](https://spiceai.org/releases/v1.11.3) (Mar 9, 2026):

* Fixed S3 metadata column projection errors when using `location`, `last_modified`, or `size` with filters or scalar functions.
* Fixed FlightSQL JDBC `Unsupported ArrowType Utf8View` errors by mapping view types to non-view equivalents.
* Fixed SQL results cache invalidation on every change-stream poll for `refresh_mode: changes` (e.g., DynamoDB Streams).
* HTTP error responses are now excluded from the cache, and a new `response_headers` column exposes response header metadata.

Spice runtime [v1.11.4](https://spiceai.org/releases/v1.11.4) (Mar 12, 2026):

* Accelerated views now support `on_zero_results: use_source` for source fallback.
* Improved S3 metadata column query robustness.

### SDKs

* **spice-dotnet** – Updated connection pooling behavior with pooled connection lifetime to improve DNS re-resolution for long-lived connections.
* **spice-java 0.6.0** – Added `reset()` support and improved transport resilience with keep-alive and DNS re-resolution improvements.

<details>

<summary>Bug Fixes</summary>

* Fixed `spice login` topbar rendering when no current org context is available.
* Fixed portal runtime errors related to deployment and dependency updates.

</details>

{% endupdate %}

{% update date="2026-02-28" %}
## February 2026

### Highlights

* **SpicepodCluster Deployments** – Deploy Spice runtime clusters with CDC-driven lifecycle management, automatic health checks, and deployment-level observability annotations.
* **Secrets Management API** – New `PUT /v1/secrets` API for managing runtime secrets, with automatic API key injection into spicepod deployments.
* **Async Queries API** – New `/v1/queries` passthrough for submitting and retrieving asynchronous query results from Spice runtime clusters.
* **Private Cluster Management** – Configure private clusters with replica limits, CPU/memory resource management, and executor scaling.
* **App Metrics API** – New `GET /v1/apps/{appId}/metrics` endpoint for CPU and memory usage metrics.
* **Onboarding** – New interactive AI onboarding path with sample datasets, suggested prompts, and step-by-step guidance.
* **SQL Playground** – Async/sync mode toggle for cluster spicepods.
* **Runtime Dataset Status** – Dataset status from the runtime is now displayed in the Datasets list.

### Runtime

Spice runtime [v1.11.1](https://spiceai.org/releases/v1.11.1) (Feb 10, 2026):

* Cayenne row-based deletion improvements and `constraints`/`on_conflict` support in Cayenne acceleration.
* DynamoDB Streams rebootstrapping for improved reliability.
* FlightSQL cookie middleware support.

<details>

<summary>Bug Fixes</summary>

* Fixed startup panics when optional services are unavailable.
* Improved health check reliability and error messaging when Spice instances are not ready.
* Fixed async query proxying to preserve response bodies.
* Fixed Spicepod status display and default SpicepodSet kind.
* Improved API error handling with typed errors.

</details>

{% endupdate %}

{% update date="2026-01-31" %}
## January 2026

### Highlights

* **Arrow v18 Upgrade** – Upgraded platform to Apache Arrow v18 with improved query performance and compatibility.
* **API Enhancements** – Updated OpenAPI spec with new server URLs and improved multi-value header forwarding.
* **Monitoring Dashboards** – Added HTTP and Flight services metrics dashboards; updated data egress, caching, and org usage monitoring charts.
* **AI Onboarding** – OpenAI credits offered on new app creation; sample data option and suggested prompts in the AI chat playground.
* **Provider Management** – Unified provider formats with category support for embeddings and catalogs.

### Runtime

Spice runtime [v1.11.0](https://spiceai.org/releases/v1.11.0) (Jan 28, 2026) — a major release:

* **Spice Cayenne (Beta)** – The Cayenne data accelerator reaches Beta with acceleration snapshots, key-based deletion vectors, and S3 Express One Zone support.
* **DataFusion v51** – SQL pipe operators (`|>`), `DESCRIBE <query>`, named arguments in SQL functions, and significant performance improvements.
* **Arrow v57.2** – 4× faster Parquet metadata parsing with a rewritten thrift metadata parser.
* **Distributed Query** – Active-active HA schedulers, mTLS for cluster communication, and cloud credential propagation to executors.
* **Prepared Statements** – Parameterized queries with query plan caching and SQL injection prevention across all SDKs.
* **Acceleration Snapshots** – Snapshot-based acceleration recovery for faster startup and rollback support.

Spice runtime [v1.10.4](https://spiceai.org/releases/v1.10.4) (Jan 5, 2026):

* Kafka/Debezium batch commit fixes and ABFSS URL support for Azure Data Lake Storage Gen2.

<details>

<summary>Bug Fixes</summary>

* Improved header forwarding reliability for multi-value headers.
* Fixed retry logic for storage volume queries with backoff.
* Fixed monaco-editor web worker for YAML editing.

</details>

{% endupdate %}

{% update date="2025-12-31" %}
## December 2025

### Highlights

* **ADBC Support** – Improved Arrow Database Connectivity (ADBC) for broader client compatibility.
* **MCP API Key Forwarding** – API key headers are now forwarded to Spice instances for `/v1/chat/completions` and `/v1/mcp/sse` endpoints, enabling authenticated MCP and chat requests.
* **Management API (api.spice.ai)** – Initial launch of the Spice management API with app and deployment management, secrets API, OpenAPI spec, and Terraform support.
* **OAuth Client APIs** – OAuth client management with scope-based access control for programmatic API access.
* **AWS Multi-Region** – New AWS us-east-1 region.
* **PAT Scopes** – Personal Access Tokens now support scoped permissions.

### Runtime

Spice runtime [v1.10.3](https://spiceai.org/releases/v1.10.3) (Dec 29, 2025):

* Improved startup reliability with async telemetry exporter initialization.
* Fixed Azure BlobFS versioned container handling.
* Fixed S3 custom endpoint query resolution.

<details>

<summary>Bug Fixes</summary>

* Fixed chat completion handling in the platform.
* Improved request validation to reject requests for deleted apps.
* Fixed spicepod logs viewer to query correct region.
* Fixed redirect loop on login.
* Improved deployment handler speed.

</details>

{% endupdate %}

{% update date="2025-11-30" %}
## November 2025

### Highlights

* **MCP API** – Added [Model Context Protocol](https://modelcontextprotocol.io/) (MCP) API endpoint with session handling, enabling integration with VS Code and other MCP-compatible clients.
* **Portal Redesign** – Redesigned login page and new website launch.
* **Log Filters** – Added log filters and pause for improved log inspection.
* **App Tags** – Added tags to App Settings for organizing applications.

<details>

<summary>Bug Fixes</summary>

* Improved platform stability and reliability.
* Fixed spicepod tags validation.

</details>

{% endupdate %}

{% update date="2025-10-31" %}
## October 2025

### Highlights

* **Iceberg Table Write Support (Preview)** – Write directly to Iceberg tables using standard SQL `INSERT INTO` commands, no Spark required.
* **Acceleration Snapshots** – Faster startup with cached acceleration states from S3.
* **Partitioned** [**S3 Vector Indexes**](../building-blocks/data-connectors/s3.md) – Improved scalability and query performance for large datasets.
* **AI SQL Function (Preview)** – Query LLMs directly in [SQL](../portal/playground/sql-query-editor.md) with `ai()` for summarization, classification, or translation.
* **Remote CLI Execution** – Run `spice sql`, `spice search`, and `spice chat` against remote endpoints.
* **Spice.js SDK v3.0.3** – Updated SDK for Node.js and browsers with simplified query APIs and better compatibility.
* **Tabbed SQL Editor** – Multi-tab SQL editor in the playground with persistent tab state.
* **SpicepodSet Support** – Spicepod deployments now support the SpicepodSet kind.
* **Search in Playground** – Search is now available directly in the playground.
* **Multi-Instance Monitoring** – Monitoring overview now shows all instances with a filter sidebar for task history.

<details>

<summary>Bug Fixes</summary>

* Improved reliability for Iceberg writes and acceleration snapshots.
* Fixed partition pruning and empty partition handling in queries.
* Improved [vector search](../use-cases/enterprise-search.md#vector-similarity-search-across-disparate-and-legacy-data-systems) stability and default limits (now up to 1,000 results).
* Improved AI [SQL query](../portal/playground/sql-query-editor.md) consistency and async handling.
* General performance and startup time optimizations across data connectors and runtime.

</details>

{% endupdate %}

{% update date="2025-09-30" %}
## September 2025

### Highlights

* [**Hybrid Search (RRF)**](../use-cases/enterprise-search.md)**:** Combine [`vector_search`](../features/search-and-retrieval.md#vector-search) and `text_search` using the new Reciprocal Rank Fusion (UDTF) for more relevant results; supports per-query rank weights, recency boosting, and configurable decay (`k` = 60.0).
* **Acceleration Metrics:** Added detailed Prometheus metrics for dataset refresh and ingestion lag:\
  `dataset_acceleration_max_timestamp_before_refresh_ms`,\
  `dataset_acceleration_max_timestamp_after_refresh_ms`,\
  `dataset_acceleration_refresh_lag_ms`,\
  `dataset_acceleration_ingestion_lag_ms`.
* **Search Defaults & Indexing:** Default result limit increased to **1,000** for both Full-Text and Vector Search; persistent on-disk FTS indexes improve reliability and restart time.
* **DataFusion v49 Upgrade:** Faster planning for queries with many columns, dynamic filters & TopK pushdown, compressed spill files, and more.&#x20;
* **Embeddings Improvements:** Support for EmbeddingGemma (Google's embedding model for text and documents) and embedding request cashing.&#x20;
* **Runtime Reliability:** Improved AI model diagnostics, clearer error messages, and stronger hybrid-query stability.
* **Observability** – Global timezone switcher, rich explain plan previews with zoom, and SQL query preview in trace history.
* **Deployment Tracking** – Deployment author and creation source are now shown in the spicepod deployments list.
* **Audit Logging** – Added audit logging for Spicepod operations.
* **GitHub Branches** – Show all GitHub branches with search in the branch selector.

<details>

<summary>Bug Fixes</summary>

* **Full-Text Search:** Fixed JOIN-level filter columns, corrected metadata projections, stabilized persistent indexes.
* **Vector Search:** Fixed embedding-column removal and improved concurrency handling.
* **Connectors:** Enhanced SQL warehouse error handling and async-query reliability.
* **Model Integrations:** Corrected regex validation; improved health-check accuracy and error clarity.
* **Query Engine:** Fixed RRF edge cases with disjoint results, removed duplicate fused entries, and improved case-sensitive column handling.

</details>

{% endupdate %}

{% update date="2025-08-31" %}
## August 2025

### Highlights

* **Amazon Bedrock Integration:** Added support for Nova-compatible models via the new Bedrock LLM provider. Configure directly in `Spicepod.yaml` using the `bedrock:` prefix and AWS credentials.
*   **AWS Redshift Support:** Connect Redshift clusters using the [PostgreSQL ](../building-blocks/data-connectors/postgres.md)data connector (`from: postgres:<schema.table>`).

    Full support for Redshift’s columnar storage and PostgreSQL-compatible types.
* **Hadoop Catalog for Iceberg:** Added support for Hadoop-based Iceberg catalogs on local (`file://`) or S3 (`s3://`, `s3a://`) storage.
*   **New** [**Data Connectors**](../building-blocks/data-connectors/)**:** Kafka Connector for real-time stream ingestion (`from: kafka:<topic>`).

    MongoDB Connector for querying NoSQL collections (`from: mongodb:<dataset>`).
* **DataFusion v48 Upgrade:** Performance improvements with DataFusion v48 — 50% smaller expression memory footprint and 10–20% faster query planning. Optimized string and window functions; up to 5.6× faster aggregate queries.
* **Model2Vec Embeddings:** Introduced optimized embeddings engine — 500× faster inference, 15× smaller model size.
* **App Storage** – New storage API and UI for managing persistent volumes.
* **App Secrets** – Edit app secrets from the portal with audit logging.
* **GPT-5 Models** – Added GPT-5 model support.
* **C# and Java SDK Samples** – Added C# and Java SDK samples to the playground.
* **Spicepod YAML Validation** – The code editor now validates Spicepod YAML in real-time.

<details>

<summary>Bug Fixes</summary>

* Fixed [Amazon S3 Vectors](../building-blocks/data-connectors/s3.md) API compatibility issue when projecting embedding columns.
* Improved AWS authentication and retry logic for Bedrock providers.
* Fixed Databricks OpenAI token compatibility.
* Improved Parquet Reader to support files missing page indexes (`parquet_page_index: auto`).
* Improved error messages and stability across data connectors and model integrations.

</details>

{% endupdate %}

{% update date="2025-07-31" %}
## July 2025

### Highlights

* **App Settings**\
  Region and storage information is now available in App Settings.
* **Spicepod Editor**\
  Editor now includes additional YAML safety checks and supports detached editing (even when repository is not connected).
* **User Experience**\
  Improved error messages, compact S3 Vectors engine input handling, and more consistent display of app/component status.

<details>

<summary>Bug Fixes</summary>

* Fixed crash handling for app config errors—users now see a specific error explaining the issue.
* Editor’s YAML validation improved, warning users before saving malformed files.

</details>

{% endupdate %}

{% update date="2025-06-30" %}
## June 2025

### Highlights

* **New Data Connectors**\
  Access [Iceberg](https://spiceai.org/docs/components/data-connectors/iceberg) and [Glue](https://spiceai.org/docs/components/data-connectors/glue) data sources directly; step-by-step documentation provided.
* **Databricks Integration**\
  OAuth for [Databricks](../integrations/databricks.md) is fully automatic; your tokens are handled behind the scenes for all catalog, SQL, and chat actions.
* **Organization Flexibility**\
  Invite members with any email connected to a GitHub account; set specific app limits (e.g., 5 apps per org for Community, 20 for Enterprise).
* **Observability**\
  Supports up to 1,000 tasks in [Observability](../features/observability/).
* **Data & Secrets**\
  [All secret](../portal/apps/secrets.md) names must be uppercase—system now enforces this for new and updated secrets.

<details>

<summary>Bug Fixes</summary>

* Query type detection for legacy SQL fixed for over 50 legacy formats.
* Organization invitation email bug resolved (GitHub-linked addresses only).
* Sticky UI headers now work in all supported browsers.

</details>

{% endupdate %}

{% update date="2025-05-31" %}
## May 2025

### Highlights

* **Smart Resource Usage**\
  Workspace owners can now set exact CPU and memory limits for each deployment. Minimum: 0.5 CPUs and 1GB RAM; Maximum: 16 CPUs and 128 GB RAM, configurable in settings.
* **Code & Editor Improvements**\
  The code editor supports copyable YAML examples, inline rendering of markdown/code blocks, and syntax highlighting for spicepod schemas.
* **Models & AI Upgrades**\
  [Claude opus-4 and Sonnet-4 ](https://spiceai.org/docs/components/models/anthropic)models are now available to all users. The model selector interface shows the version number and model status.
* **Editor Experience**\
  Live YAML validation and safety features have been implemented—invalid configurations are flagged instantly before saving.

<details>

<summary>Bug Fixes</summary>

* Editor no longer drops indentation or list formatting when pasting code blocks.
* Model names and token handling have been corrected for export and import.

</details>

{% endupdate %}

{% update date="2025-04-30" %}
## April 2025

### Highlights

* **Resource Management**\
  Users can now set and adjust storage quotas up to 256 GB per workspace, and view usage directly from the app’s dashboard. Storage alerts notify you once you reach 90% of quota.
* **AI Model Support**\
  Added [Grok-3 series](https://spiceai.org/docs/components/models/xai) and [GPT-4.1](https://spiceai.org/docs/components/models/openai) support for all users. Models are selectable in the Spicepod and chat interface as soon as they become available.
* **Activity Analytics**\
  [Integration with Google Analytics](/broken/pages/HfZAhJ1SCZnNoCGiEtOa) allows users to track app activity (sessions, completions, and installations) directly in the usage dashboard. UTM parameters for app installs are automatically recorded.
* **User Interface Experience**\
  Simplified error messages and cleaned up UI for benchmark reports, progress views, and data display.

<details>

<summary>Bug Fixes</summary>

* Benchmarks section now returns exact inferences count for up to 30 days of data.
* Fixed occasional YAML validation errors in the Spicepod editor.

</details>

{% endupdate %}

{% update date="2025-03-31" %}
## March 2025

### Highlights

* **System Performance**\
  Concurrency limits raised to 100 parallel active queries per workspace. Workspace owners can now monitor live query queues.&#x20;
* **Custom Query Handling**\
  [Query arguments](sql-reference.md) included in all GET requests are now passed transparently to downstream systems for more granular filtering and control.
* **AI Chat Logs & History**\
  Improved loading and navigation for chat logs; message skeletons, progress bars, and artifact previews added. Message history now supports up to 500 prior messages.
* **Monitoring Enhancements**\
  [New app and model performance views](../portal/monitoring-and-request-logs.md) allow users to see live status and recent activity for the apps and models they own.

<details>

<summary>Bug Fixes</summary>

* Resolved autoscroll issues when reviewing long chat histories.
* Fixed metric display errors for query queues when over 100 items were present.

</details>

{% endupdate %}

{% update date="2025-02-28" %}
## February 2025

### Highlights

* **AI Chat & Playground**\
  New session handling: users can revisit prior conversations and access a catalog explorer from [Playground](../portal/playground/ai-chat.md). Improvements to chat UI display make longer chat sessions and code completions easier to review. Markdown and progress rendering are now supported in chat.
* **Spicepod Editor**\
  Choose from multiple AI models and tools directly in the editor sidebar. YAML safety checks alert users about indentation or formatting issues before saving.

<details>

<summary>Bug Fixes</summary>

* Fixed chat session loading delays for conversations over 50 messages.
* Dashboard graphs now update within 10 seconds of new data becoming available.
* Resolved error messages when switching between model and tool selections in the Spicepod editor.

</details>

{% endupdate %}

{% update date="2025-01-31" %}
## January 2025

### Highlights

* **Spicepod Experience Improvements**\
  When deploying an app, the system automatically selects the latest available patch version for your Spicepod (e.g., 1.0.3 will be chosen if your app currently uses 1.0.2). The Spicepod editor now provides clearer navigation and indicates which version is being deployed.
* **Performance & User Interface**\
  Member management, dataset, and model lists in the user interface are now more responsive and display item status in real time.

<details>

<summary>Bug Fixes</summary>

* Deployment flow issues resolved for navigating between editing and deploying a Spicepod.
* Improved error feedback when users enter invalid configuration values.

</details>

{% endupdate %}

{% update date="2024-12-31" %}
## December 2024

### Highlights

* **Spicepod.yaml Configuration**\
  [Spicepods](../portal/app-spicepod/spicepod-configuration.md) now display unique content identifiers (CIDs) for each version deployed, and retaining CID tracking improves history and rollback accuracy. The editor sidebar is more intuitive, supports flexible editing even if no repository is connected, and surfaces volume claim template types.
* **Status & History**\
  Deployment status, history, and new monitoring tab allow users to see all current and prior deployments and their identifiers.
* **User Interface Updates**\
  Improved empty state displays, clearer descriptions, and smarter menu layouts for public apps. Datasets, app code, and models now have more granular state visibility.

<details>

<summary>Bug Fixes</summary>

* Fixed anonymous viewer errors for public app pages.
* Fixed edge case in flavor selection and visibility for newly created Spicepods.

</details>

{% endupdate %}

{% update date="2024-11-30" %}
## November 2024

### Highlights

* **Dashboard Expansion**\
  Added the Models Benchmarks dashboard, including reference scores for popular datasets. Refined UI for performance and alert dashboards, ensuring users see the correct score for each model and configuration on every run.
* **Spicepod Management**\
  You can now view the full list of your managed spicepods, with creation, update, and removal activity tracked in the app. Use the `/v1/activity` endpoint to see app activity logs by timestamp.&#x20;
* **User Experience Improvements**\
  Streamlined editor: new saving, reset and validation logic. Deleted apps automatically remove their attached spicepods. Default datasets and version fallback logic improved for all new apps.

<details>

<summary>Bug Fixes</summary>

* Improved dashboard data accuracy for benchmarks and metrics.
* Fixed bug with SQL result filters that caused stale data to appear in model dashboards.
* Activity and log endpoints now reliably return the most recent 1000 entries.

</details>

{% endupdate %}

{% update date="2024-10-31" %}
## October 2024

### Highlights

* **Copilot & Spicepod Integration**\
  [The Copilot onboarding flow](../integrations/github-copilot.md) is now unified and leverages shared spicepod instances for organizational consistency. Deployment status and logs are directly viewable in the Copilot UI.
* **Editor Enhancements**\
  The code editor now highlights YAML formatting errors and enables editing even when a repository isn’t connected. Sample dataset previews and component mapping have been improved. Side panel includes quick access to all relevant settings.
* **Model & Activity Tracking**\
  The models view now shows the current version, tool status, and last update date. Only relevant models are visible; deprecated items are automatically hidden.

<details>

<summary>Bug Fixes</summary>

* Fixed chat history scroll and response display for long sessions.
* Editor bugs related to line numbering, case handling, and switching between specs have been resolved.
* Fixed edge cases in deployment status and activity notifications.

</details>

{% endupdate %}

{% update date="2024-09-30" %}
## September 2024

### Highlights

* **Operator Scale Set**\
  Spicepods now support explicit scale configuration: users can specify up to 50 concurrent instances per spicepod.
* **User Flows**\
  All setup flows are now handled from a streamlined single location, with improved deployment selection and history.
* **Observability Dashboards**\
  [Model and dataset metrics](https://spiceai.org/docs/features/observability) are now presented with sortable columns and trendline charts for the last 60 days. Task history and dataset refresh logic have been made transparent.

<details>

<summary>Bug Fixes</summary>

* Fixed activity endpoint latency for organizations with more than 25 datasets.
* Improved logging to prevent duplicate error messages in UI.
* Fixed model filtering and sorting in deployment lists.

</details>

{% endupdate %}

{% update date="2024-08-31" %}
## August 2024

### Highlights

#### Features

* **Schema Integration**: Added functionality to pipe `GetSchema` from API to spiced backend when using PATH type.
* **Enhanced Spicepod Functionality**: Streamlined setup flow using `spicepod.yaml` from GitHub and support for custom registries and images. Improved task history monitoring and deployment logic.

#### Reliability

* **Error Handling**: Improved error handling in Copilot agent and chat completions.

#### User Interface Updates

* **SQL Playground Improvements**: Enabled new features such as default queries, table formatting, and JSON parsing. Updated SQL Explorer with a streamlined layout and new tools.
* **Chat and Layout Enhancements**: Added multiline chat input, improved layout consistency, and table formatting in chat.

<details>

<summary>Bug Fixes</summary>

* Fixed chat completion streaming and cleaned up Copilot agent handler.
* Updated SQL Explorer and trace views with better handling of inputs, outputs, and errors.

</details>

{% endupdate %}

{% update date="2024-07-31" %}
## July 2024

#### New Features

**Spicepod Deployment Enhancements:**

* Added support for deploying Spicepods directly from GitHub repositories.
* Introduced new flows for provisioning and configuring Spicepods, including updated formats and integration with external connections.
* Unified Spicepod setup for both general and GitHub Copilot-specific use cases.

**AI Chat Playground Updates:**

* Launched an improved playground for AI chat applications.
* /v1/chat/completions now supports streaming responses compatible with OpenAI SDK.

**SQL Explorer:**

* Introduced a unified SQL Explorer playground with enhanced query capabilities and debugging tools.
* Improved SQL support for managed spiced instances, including better routing and error handling.
* Added SQL error marker detection with line and column details.

#### **Enhancements**

**UI and UX Refinements:**

* Updated rounded corners, button radii, and other style tweaks for a more consistent look.
* Enhanced chat UI and added new interactive features like chat spinners and error markers for SQL.

#### **Bug Fixes**

* Fixed inconsistencies in Copilot configuration, ensuring seamless provisioning and deployment.

{% endupdate %}

{% update date="2024-06-30" %}
## June 2024

The June release included a [C# SDK](../sdks/dotnet-sdk.md) for Spice AI and Spice OSS to allow for development using .Net Standard 2.0 and .Net 6.0 or greater.

**Changes**

1. \[Dotnet SDK] Released v0.1.0
2. \[Platform] Performance, reliability, and security improvements.

{% endupdate %}

{% update date="2024-05-31" %}
## May 2024

The May release included [EigenLayer Delegation Manager datasets.](/broken/pages/szea7RdCSxE1KLlrSpca)  The datasets can be explored in the portal Playground and queried via SQL.

**Changes**

1. \[Data] SQL Datasets for Eigenlayer now include Delegation Manager datasets.
2. \[Platform] Performance, reliability, and security improvements.

{% endupdate %}

{% update date="2024-04-30" %}
## April 2024

The April release included updates to the Spice SDK's to support querying a local [Spice Runtime](https://github.com/spiceai/spiceai) without requiring a Spice.ai API key.  Details and examples can be found in the [Rust](../sdks/rust-sdk/), [Go](../sdks/go.md), [Python](../sdks/python-sdk/), and [Node.js](../sdks/node.js-sdk/) SDK documentation.

**Ethereum Holesky nodes were shut down and datasets were removed on Thursday, April 25th at 8pm PST.**

Holesky JSON RPC and Beacon API's requests to **/holesky** will return a 404 Not Found response and Holesky SQL datasets are no longer available.

**Changes**

1. \[Rust SDK] Released v2.0.0
2. \[Node.js SDK] Released v2.0.0
3. \[Go SDK] Released v6.0
4. \[Python SDK] Released v2.0
5. \[API] Removed Holesky JSON-RPC API
6. \[Data] Removed Holesky datasets
7. \[Platform] Performance, reliability, and security improvements.

{% endupdate %}

{% update date="2024-03-31" %}
## March 2024

**Ethereum Goerli nodes were shut down on Friday March 22 at 5pm PST.**

The Ethereum Foundation planned to stop supporting Goerli [one month after Dencun](https://nam12.safelinks.protection.outlook.com/?url=https%3A%2F%2Fspice.us5.list-manage.com%2Ftrack%2Fclick%3Fu%3De853435dd452a4d343ea1133d%26id%3D1f0eddc002%26e%3D9d81a8fb5f\&data=05%7C02%7Cluke%40spice.ai%7C2ecaa574022c4aac7f8c08dc4833dd51%7C925431232b6a4eec9b6f595720cd1c8f%7C0%7C0%7C638464634819771165%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C0%7C%7C%7C\&sdata=6xvJI%2Fdn210TQ7ejvHXrdNTQCH7wkUF2ARQ8JHXCJ1c%3D\&reserved=0) was activated on Ethereum mainnet, which would be April 13th, however nearly all Goerli Validators were already shut down, effectively shutting the network down prematurely. Spice AI is shutting down nodes early, aligned to other providers decisions to also shut down, such as [Alchemy](https://nam12.safelinks.protection.outlook.com/?url=https%3A%2F%2Fspice.us5.list-manage.com%2Ftrack%2Fclick%3Fu%3De853435dd452a4d343ea1133d%26id%3D351e541bfd%26e%3D9d81a8fb5f\&data=05%7C02%7Cluke%40spice.ai%7C2ecaa574022c4aac7f8c08dc4833dd51%7C925431232b6a4eec9b6f595720cd1c8f%7C0%7C0%7C638464634819780937%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C0%7C%7C%7C\&sdata=HAX45r3B5W95bt3JRpQvgoGN1Q7brf4yFHNtdYk4%2BKg%3D\&reserved=0).

After the shutdown, JSON RPC and Beacon requests to **/goerli** returned 404 Not Found and Goerli SQL datasets, while still available for query, are no longer being updated, including Firecache datasets.

[Goerli SQL Tables](https://nam12.safelinks.protection.outlook.com/?url=https%3A%2F%2Fspice.us5.list-manage.com%2Ftrack%2Fclick%3Fu%3De853435dd452a4d343ea1133d%26id%3D8eb3381cbc%26e%3D9d81a8fb5f\&data=05%7C02%7Cluke%40spice.ai%7C2ecaa574022c4aac7f8c08dc4833dd51%7C925431232b6a4eec9b6f595720cd1c8f%7C0%7C0%7C638464634819763981%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C0%7C%7C%7C\&sdata=I%2BdPMW%2FBgLUJXMio7efzCxu3l2R1ju9RLE%2FcvxK0w54%3D\&reserved=0) will remain available for historical query purposes.

The [Ethereum](/broken/pages/nLS2MJKcNRnMbanBVJUv) and [Beacon](/broken/pages/8JwabwGgl0JmUvtCO5Su) datasets now include fields from the Cancun and Dencun network upgrade.

**Changes**

1. \[Rust SDK] Released v1.0.3
2. \[API] Removed Goerli JSON-RPC API
3. \[Data] SQL datasets for Ethereum and Beacon chains now include Cancun and Dencun fields.

{% endupdate %}

{% update date="2024-02-29" %}
## February 2024

The February release focused on stability and performance improvements.

**Changes**

1. \[Platform] Performance, reliability, and security improvements.

{% endupdate %}

{% update date="2024-01-31" %}
## January 2024

The January release added support for the Ethereum [Holesky](https://github.com/eth-clients/holesky) testnet in preview. Holesky data can be explored in the portal Playground, queried by [SQL](/broken/pages/KGqUTzMzBWIdFzG1BHDh) and Firecache, and made available to applications by JSON-RPC [API](/broken/pages/3f8HPEKNoLHoVlikewdd). &#x20;

<figure><img src="../.gitbook/assets/image (37).png" alt=""><figcaption><p>Holesky Testnet APIs and datasets available in preview</p></figcaption></figure>

**New in this release**

1. \[Platform] Added Ethereum [Holesky](/broken/pages/KGqUTzMzBWIdFzG1BHDh) testnet data and API's.
2. \[Platform] Achieved [SOC 2 Type II compliance](../security/security.md).

**Changes**

1. \[Platform] Performance, reliability, and security improvements.
2. \[Node JS SDK] [Released v1.0.2](https://github.com/spiceai/spice.js/releases/tag/v1.0.2).

{% endupdate %}

{% update date="2023-12-31" %}
## December 2023

The December release focused on stability and performance improvements.

**Changes**

1. \[Platform] Performance, reliability, and security improvements.

{% endupdate %}

{% update date="2023-11-30" %}
## November 2023

The November release focused on [External Data Sources](../portal/external-data-sources.md) and overall performance improvements following the [General Availability (GA)](https://blog.spice.ai/spice-ai-is-generally-available-d76c4289960c) release in October.

You can now connect external data sources like PostgreSQL and MySQL to Spice.ai, which are then available to query with SQL including joining with any other Spice.ai dataset in the Spice.ai platform.

<figure><img src="../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption><p>New Data Source dialog for connecting external data sources.</p></figcaption></figure>

Navigation is also easier with the addition of the Command Bar. Simply use **Cmd+K** on Mac or **Windows+K** in Windows to quickly navigate the portal by keyboard! Give it a try!

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption><p>The new navigation command bar.</p></figcaption></figure>

**New In this Release**

1. \[Portal] Added PostgreSQL and MySQL external data sources. See the [docs](../portal/external-data-sources.md) for details.
2. \[Portal] Added Dark Mode! Configure via [Profile Settings](../portal/profile/#dark-mode).
3. \[Portal] Added Command Bar navigation.
4. \[Data] Added [`eth.wallet_lst_balances`](/broken/pages/H9cSfmiXpD7DEg8uDBkJ) including rETH, stETH, and cbETH LSTs.
5. \[Firecache] Upgraded to DuckDB 0.92.
6. \[Platform] Performance, reliability, and security improvements.
7. \[Go SDK] Released v3.0.0, [learn more](https://github.com/spiceai/gospice/releases/tag/v3.0.0).

{% endupdate %}

{% update date="2023-10-31" %}
## October 2023

Spice.ai is now generally available! [Read the announcement](https://blog.spice.ai/spice-ai-is-generally-available-d76c4289960c).

General Availability launches a brand-new collaborative developer experience, v1 APIs and SDKs, over [100 open-source community](https://github.com/spiceai/datasets) and [EigenLayer](/broken/pages/szea7RdCSxE1KLlrSpca) datasets, and a removal of the waitlist. Any developer can now sign-up and use Spice.ai with one of the new [pricing plans](../pricing/plans.md), including the $99 developer plan and the complementary [Community Edition](/broken/pages/TswKQuxvMpeSWfvqFhxa).

**New In this Release**

1. \[HTTP API] Add v1 API:
   1. [SQL Query API ](../api/sql-query/http-api.md)
   2. [Prices API](/broken/pages/S6tHtEgbsxtkFBnX2RHy)
   3. [Gas Fees API](/broken/pages/FHKcRCIcE0WOJCsGUnlq)
   4. [Predictions API](/broken/pages/2C09l89bwNrM5jRF0xSx)
2. \[Portal] New Portal and developer experience, without waitlist limitations.
3. \[Portal] New pricing plans are available, [learn more](https://docs.spice.ai/getting-started/pricing).
4. \[Portal] Datasets and Views generally available, [learn more](release-notes.md#datasets-and-views).
5. \[Firecache] Upgraded to DuckDB 0.91.
6. \[Functions] Upgraded to DuckDB 0.91.
7. \[Ethereum] Added support for `eth_maxPriorityFeePerGas` and `trace_filter` to JSON RPC API.
8. \[Ethereum] Expanded Ethereum client diversity by adding [Reth](https://github.com/paradigmxyz/reth) support.
9. \[Ethereum] Expanded Polygon client diversity by adding Erigon support.
10. \[Python SDK] v1.0.0 released, [learn more](https://github.com/spiceai/spicepy/releases/tag/v1.0.0).
11. \[Go SDK] v3.0.0 released, [learn more](https://github.com/spiceai/gospice/releases/tag/v3.0.0).
12. \[Node.js SDK] v1.0.0 released, [learn more](https://github.com/spiceai/spice.js/releases).
13. \[Rust SDK] v1.0.1 released, [learn more](https://github.com/spiceai/spice-rs/releases/tag/v1.0.1).

{% endupdate %}

{% update date="2023-09-30" %}
## September 2023

In the September release, Crypto/Token Prices data has been significantly expanded and improved upon.

Historical and latest prices are available via [REST API](/broken/pages/S6tHtEgbsxtkFBnX2RHy) and [SQL Query](/broken/pages/zXpdOConoLcf9VN61mY5).

The number of token pairs supported by the REST API has greatly increased. In addition, if a pair does not have swap data, the platform will attempt to calculate a synthetic price for the pair, routed via swaps across different exchanges. For example, if a price for the pair BAO-AUD does not exist, but the pairs BAO-USDT and USDT-AUD do, then the API will calculate the routed price across the two swaps to determine a synthetic price, which will be returned in the payload as the `spiceai` price. The API will use data cross both centralized and decentralized exchanges. This enables pricing data where it was not possible before.

Additional tables have been added for token prices and assets.

* [prices.assets](/broken/pages/TfcC8V5XWaq9Vy5yz8du) list all assets tracked and available via SQL Query. 8,856 assets are currently available.
* [prices.all\_pairs](/broken/pages/SHY0O3esX5xy1WapCz4s) list all the token pairs with prices available via SQL Query. 3,342 pairs are currently available.

In addition to the existing [prices.\[tokenA-tokenB\]](/broken/pages/8bSm6urWvwm7YFuhgfN5) a table of prices for each token pair.

Note: In both cases, the list is a subset of what is available via the REST API.

**New In this Release**

1. \[SQL Query] Added `prices.assets` table.
2. \[SQL Query] Added `prices.all_pairs` table.
3. \[Prices API] Added swap routed token pricing.
4. \[Prices API] Enhanced coverage of token pairs.
5. \[Portal] Bugfixes and minor improvements to the UI.

{% endupdate %}

{% update date="2023-08-31" %}
## August 2023

The August release expands the functionality of [Spice Functions](/broken/pages/aBmaUV0vHCWOQ4ioYk6o) (beta) and [Datasets and Views](../portal/datasets-and-views.md).

#### Spice Functions

{% hint style="info" %}
Spice Functions is in beta and initially available for Design Partners. Get in touch for more info.
{% endhint %}

Developers can now author [Spice Functions](/broken/pages/aBmaUV0vHCWOQ4ioYk6o) in [Golang](/broken/pages/DbdqJ1WzZbndCfju4Yi0) with the Go1.x runtime in addition to [Python](/broken/pages/KjlSDA3olIHJPXR5Ycbh).

Combine Spice Functions with expanded Datasets and Views support to generate custom datasets "ETL-less" in the Spice platform that can be kept private or shared with others.

#### Datasets and Views

Spice now supports SQL migrations to enable creation of custom user-authored datasets and views.

**New In this Release**

1. \[Spice Functions] Add Go1.x runtime.
2. \[Datasets and Views] Add SQL migrations.
3. \[Datasets and Views] Add sharing and publishing.
4. \[Portal] Add basic charting for SQL and Firecache results.
5. \[Portal] Bugfixes and minor improvements to the UI.

{% endupdate %}

{% update date="2023-07-31" %}
## July 2023

The July release enables new compute over data capability with [Spice Functions](/broken/pages/aBmaUV0vHCWOQ4ioYk6o) including application data storage, the ability to define and create custom datasets, and deep integration with GitHub.

#### Spice Functions

{% hint style="info" %}
Spice Functions is in beta and initially available for Design Partners. Get in touch for more info.
{% endhint %}

[Spice Functions](/broken/pages/aBmaUV0vHCWOQ4ioYk6o) is a hosted compute experience that enables developers to write code in their preferred language and run it on the Spice platform, co-located with Spice data.

See [Broken link](/broken/pages/aBmaUV0vHCWOQ4ioYk6o "mention") for full documentation on creating and deploying Spice Functions.

#### Spice Firecache

{% hint style="info" %}
Spice Firecache is in beta and initially available for Design Partners. Get in touch for more info.
{% endhint %}

[Spice Firecache](../features/federated-sql-query.md) is a real-time SQL cache that enables developers to cache Spice datasets for high concurrency, blazing fast SQL query up to 10x the performance of general SQL query.

The Spice platform completely manages the Spice Firecache data lifecycle, ensuring datasets are real-time updated on triggers like new blocks and with stale data being automatically evicted.

See [federated-sql-query.md](../features/federated-sql-query.md "mention") for full documentation on deploying datasets to the Spice Firecache.

#### Custom Datasets

Spice now supports the definition and creation of your own private Datasets and Views, which can then be queried with SQL, cached in Spice Firecache, and published publicly to be shared with others.

See [datasets-and-views.md](../portal/datasets-and-views.md "mention") for full documentation on creating private Datasets and Views.

#### Connecting Github Repos

Connecting your Spice.xyz app to a GitHub repository allows you to create custom Datasets and Views, add datasets to Spice Firecache, author Spice Functions, and train AI Models.

See [connect-github.md](../portal/apps/connect-github.md "mention") for full documentation on connecting your Github Repo to a Spice App.

**New In this Release**

1. \[Platform/Portal] Support for compute over data with Spice Functions&#x20;
2. \[Platform/Portal] Real-time SQL caching through Spice Firecache
3. \[Platform/Portal] Support for custom datasets and views beyond those curated by Spice
4. \[Portal] Connect your Github repo to manage all your Spice code with version control
5. \[Flow] RPCs can now be sent to the Spice Flow node using the [Flow Access API](https://developers.flow.com/concepts/nodes/access-api)
6. \[Flow] Added datasets detailing transfers of NFTs and tokens on the Flow blockchain, for more details see [Broken link](/broken/pages/G6NYPF18ou1tMQpmQ0PU "mention")

**Changes**

1. \[Portal] Any users you add to your org that is on a Pro or Partner plan will now automatically be activated and removed from the Spice.xyz waitlist.

{% endupdate %}

{% update date="2023-06-30" %}
## June 2023

The June release includes new datasets for Ethereum/Goerli wallet balances + Flow core/nft datasets, additional platform performance upgrades along with several usability and user-experience improvements.

#### Wallet Balance Datasets

The wallet balances datasets contains a block-level view of all Ether balance changes due to gas fees, withdrawals, and committed external and internal (contract) transactions.

{% hint style="info" %}
Wallet Balances datasets do not yet have full historical data.
{% endhint %}

* [`eth.wallet_balances`](/broken/pages/2SrRafV0ZWlvWnPA4xZQ)
* [`goerli.wallet_balances`](/broken/pages/ZDflHe148WqmIB9aqwLo)

#### Flow Datasets

[Flow](/broken/pages/0app6ZAzlVznTC9fkIv2) is a fast, decentralized, and developer-friendly blockchain, designed as the foundation for a new generation of games, apps, and the digital assets that power them. Supercharge your Flow apps with these datasets indexed by Spice.xyz.

{% hint style="info" %}
Flow support is in preview. Contact us to let us know if Flow support is important to you!
{% endhint %}

* [`flow.blocks`](/broken/pages/YVeXYU4WmaPd565Tntc0)
* [`flow.transactions`](/broken/pages/iMYEbh5MIFpZxtM0BJDZ)
* [`flow.events`](/broken/pages/ohZTimfj9N7FsJ34IjMr)
* [`flow.nfts`](/broken/pages/ppt5H1UNxAzUZVoRik9P)
* [`flow.nft_transfers`](/broken/pages/6LrEnUAavrsFKrZo7SlV)
* [`flow.token_transfers`](/broken/pages/k3FdCsd7c8YahZSNUzGv)

#### Portal

Portal UX improvements include the ability to search the dataset reference for specific datasets and inserting a sample query with a click. GitHub avatars now show up in the organization drop-down, as well as a distinction between your personal account and other organizations you are part of. Searching for members to add to an organization now properly autocompletes.

{% embed url="https://youtu.be/2qk8ZMZOJek" %}
[Spice.xyz](http://spice.xyz) SQL explorer dataset reference improvements
{% endembed %}

**New In this Release**

1. \[Wallet Balances] Added wallet balance datasets for Ethereum and Goerli which track changes to wallet balances on every block.
2. \[Flow] Added Flow datasets, including core and enriched NFT/token datasets.
3. \[Portal] Show GitHub avatars in the organization drop-down.
4. \[Portal] The organization drop-down now distinguishes between your personal account and other organizations you are part of.

**Changes**

1. \[Portal] You can now search for datasets in the dataset reference.
2. \[Portal] Sample queries can be injected into the query editor by clicking in the dataset reference.
3. \[Portal] Improved error messages in the SQL editor for syntax errors.
4. \[Portal] The dialog box used for adding organization members now auto-completes for potential members to add.
5. \[Performance] Improved performance for several datasets, including `eth.beacon.validators` and `goerli.beacon.validators`.

{% endupdate %}

{% update date="2023-05-31" %}
## May 2023

The May release includes significant platform performance upgrades with the rollout of Spice AI's **second generation platform architecture (Gen. 2)** along with several usability and user-experience improvements.

[Ethereum](/broken/pages/nLS2MJKcNRnMbanBVJUv) and [Polygon](/broken/pages/iyngGYSqtXIiSELjLtA5) block latency (the time to process a block and make it available for query) is now **subsecond** for core data like blocks, transactions, and logs, and less than **2.5 seconds** for enriched data like [NFTs](/broken/pages/ZErqCI8yrvc9N9MLiR2b), [ENS](/broken/pages/BPicAdfXhg6ZdvP21FJ5), [Uniswap](/broken/pages/cuTP7dY2UDHfe4l0HlUq), and [Sushiswap](/broken/pages/GjiVdvTeA1UVRPs0hXsM). Query execution speed is now **1.5x-10x faster!**

<figure><img src="../.gitbook/assets/image (18).png" alt=""><figcaption><p><a href="/broken/pages/Qr9ifd9IY5E75sX5VPh9">Example Queries</a> referenced in docs are now <strong>1.5x-10x faster!</strong></p></figcaption></figure>

Portal UX improvements include the ability to collapse the sidebar, resize the SQL query input and results pane, and expand to a fullscreen focus mode. SQL query errors are now highlighted with red underline swiggles along with a more readable error pane. Finally, results render faster and app switching is faster.

{% embed url="https://youtu.be/ZJNt3paE7hA" %}
[Spice.xyz](http://spice.xyz) SQL explorer fullscreen mode
{% endembed %}

**New In this Release**

1. \[Platform] Spice AI's Second-Generation Platform Architecture (Gen. 2) resulting in 30-50% data latency improvements, 1.5x-10x faster queries, with a target availability of 99.9%.
2. \[Portal] SQL query editor contextual error reporting.
3. \[Portal] SQL query explorer fullscreen mode.
4. \[Portal] SQL query editor and results pane resizability.
5. \[Portal] Collapsable sidebar.
6. \[Portal] Page loading progress bar.

**Changes**

1. \[Portal] The portal now uses a [GitHub App](https://docs.github.com/en/apps) to integrate with GitHub. This will result in a request for re-authorization on the next sign-in.
2. \[Go SDK] [gospice v0.2.1](https://github.com/spiceai/gospice) released with [Prices API](/broken/pages/pPqfNHXdBsKFsmclcUaV) and [Apache Arrow v12](https://pkg.go.dev/github.com/apache/arrow/go/v12) support.
3. \[Node.js SDK] [spice.js v0.3.2](https://github.com/spiceai/spice.js/releases) released with a fix to re-support Node 16.

{% endupdate %}

{% update date="2023-04-30" %}
## April 2023

This month we added new datasets including Chainlink prices data feeds, Aave v2 contract data, Ethereum Shanghai/Capella beacon and withdrawals data, and we now fully index the Goerli testnet. We also released the Goerli Beacon and Ethereum Beacon APIs. \
\
**Chainlink Data Feeds:** You can now leverage oracles for various data feed on the Chainlink network by querying prices specific tables including `eth.chainlink.prices` and `eth.chainlink.recent_prices`.

This includes accessing the latest, minute-precision data on `transaction_hash`, `oracle_address`, `block_timestamp`, price of the asset in WEI, `asset_address` and more. The columns and their schema available for each table can be viewed with the describe \<table> command:

<pre class="language-sql"><code class="lang-sql"><strong>/* Show the columns available */
</strong>DESCRIBE eth.chainlink.prices;
</code></pre>

Data for for [Curve](https://classic.curve.fi/whitepaper), [Balancer](https://balancer.fi/), [Aave](https://aave.com/), [Yearn](https://yearn.finance/), [DAI](https://makerdao.com/en/), [Tether USD](https://tether.to/en/about-us/) and 30 other top assets is available. See the full list of supported data feeds [here](https://docs.spice.xyz/reference/sql-query-tables/sql-query-tables/chainlink-tables/prices-tables).&#x20;

\
**Aave V2 Dataset:** In addition to the Aave token prices data feed on the Chainlink network, we have released indexed [Aave V2](/broken/pages/JmNf5vtOxn9GC7qHCisC) data for loans, collaterals and prices. You can query for the latest updates to Aave loans and collateral by querying `eth.aave_v2.loan_updates` and `eth.aave_v2.collateral_updates`.&#x20;

<figure><img src="../.gitbook/assets/Screen Shot 2023-05-09 at 9.49.27 AM.png" alt=""><figcaption><p>Latest view of all Aave V2 loans query, sample output</p></figcaption></figure>

\
**Ethereum Shanghai/Capella Upgrade and Withdrawals Datasets:** The [Ethereum Shanghai/Capella upgrade](https://blog.ethereum.org/2023/03/28/shapella-mainnet-announcement) is now full supported after its completion on April 12, 2023, 10:27:35pm UTC at epoch 194048.

The completion of Ethereum's Shanghai hard fork, also known as "Shapella," has authorized the withdrawal of funds for individuals who have "staked" their ETH to validate and secure blockchain transactions.

We also released the `eth.withdrawals` dataset to capture new withdrawals. For example, you can now query to get ETH withdrawn:

```sql
with eth_withdrawn as (
  select eth.withdrawals.block_number, SUM(amount) as amount_gwei
  from eth.withdrawals
  group by eth.withdrawals.block_number
  order by block_number asc)

select AVG(amount_gwei / 1e9) as amount_eth
from eth_withdrawn
```

<figure><img src="../.gitbook/assets/Screen Shot 2023-05-10 at 10.20.00 PM.png" alt=""><figcaption><p>Get ETH withdrawn after the Ethereum Shanghai upgrade, sample output</p></figcaption></figure>

Explore detailed sample SQL queries for [DEX](/broken/pages/ILJgG6I3h4WEEH4nPbcA), [Cross-Chain](/broken/pages/6RadYv5EAfvtPqVQJAme), [Ethereum](/broken/pages/QQSPO1wNT4F9NsfQKzQd), [Beacon](/broken/pages/Qr9ifd9IY5E75sX5VPh9), and [Bitcoin](/broken/pages/YphzWK8SmAbgWoDBCGMW) in the Spice Docs.<br>

**Goerli Testnet:** Full support for the Goerli testnet has been added, including hosted beacon and execution nodes, and fully indexed datasets, helping developers test their applications before mainnet launch.&#x20;

Available with realtime and historical data on [Spice.xyz](https://spice.xyz/), this includes Goerli base type tables, Goerli [token specific tables](https://docs.spice.xyz/reference/sql-query-tables/goerli/token-tables), and Goerli [Beacon Chain specific tables](https://docs.spice.xyz/reference/sql-query-tables/goerli/beacon-chain-tables). You can start by querying `goerli.traces`, `goerli.blocks` or a number of sample beacon queries.&#x20;

For example, you can query from `goerli.beacon.validators` to retrieve recently updated validators, with their balance and status:

<figure><img src="../.gitbook/assets/Screen Shot 2023-05-09 at 8.33.15 PM.png" alt=""><figcaption><p>Recently updated validators, with their balance and status by querying from <code>goerli.beacon.validators</code>, sample output</p></figcaption></figure>

**Goerli Beacon API:** You can easily retrieve requests like "[get validator balances from state](https://docs.spice.xyz/api/goerli/beacon-http-api)" using the **`https://data.spiceai.io/goerli/beacon`** API, which is compatible with the read-only GET requests from the [Beacon Node API specification](https://ethereum.github.io/beacon-APIs/).

All [Goerli testnet data APIs](/broken/pages/ZH7I0D6n3CAQkAjJPB2i) are available at **/goerli/v0.1** including the following:

* [JSON RPC Methods](/broken/pages/LoF0K9hGwCUsufHFL99c)
* [Beacon HTTP API](/broken/pages/fw7PSsDUjk2312IkDcgQ)
* [Blocks](/broken/pages/XBjZvzOO9SsXcSnSbLjp)
* [Contracts](/broken/pages/0Dd11fCEPKWHVO8fG7YG)
* [Gas Fees](/broken/pages/cF1fACaign3olU4TtGai)

**Ethereum Beacon API:** The [Ethereum API](/broken/pages/mqB7Hs5dPwenu4OtY6QX) is now updated to support Beacon requests via the **`https://data.spiceai.io/eth/beacon`** API. This is compatible with the read-only GET requests from the [Beacon Node API specification](https://ethereum.github.io/beacon-APIs/).&#x20;

See the full list of Beacon API requests [here](https://docs.spice.xyz/api/ethereum/beacon-http-api).&#x20;



**New In this Release**

* \[Goerli] added `goerli.blocks`, `goerli.recent_blocks`&#x20;
* \[Goerli] added `goerli.transactions`, `goerli.recent_transactions`&#x20;
* \[Goerli] added `goerli.logs`, `goerli.recent_logs`&#x20;
* \[Goerli] added `goerli.traces`, `goerli.recent_traces`&#x20;
* \[Goerli] added `goerli.withdrawals`, `goerli.recent_withdrawals`&#x20;
* \[Goerli] added `goerli.contracts`&#x20;
* \[Goerli] added `goerli.tokens`
* \[Goerli] added `goerli.token_transfers`
* \[Goerli] added `goerli.tokens_erc20`
* \[Goerli] added `goerli.token_transfers_erc20`
* \[Goerli] added `goerli.tokens_erc721`
* \[Goerli] added `goerli.token_transfers_erc721`
* \[Goerli] added `goerli.tokens_erc1155`
* \[Goerli] added `goerli.token_transfers_erc1155`
* \[Goerli] added `goerli.token_mints`
* \[Goerli] added `goerli.recent_token_mints`
* \[Goerli] added `goerli.beacon.slots`, `goerli.beacon.recent_slots`
* \[Goerli] added `goerli.beacon.voluntary_exits`, `goerli.beacon.recent_voluntary_exits`
* \[Goerli] added `goerli.beacon.attestations`, `goerli.beacon.recent_attestations`
* \[Goerli] added `goerli.beacon.attester_slashings`, `goerli.beacon.recent_attester_slashings`
* \[Goerli] added `goerli.beacon.deposits`, `goerli.beacon.recent_deposits`
* \[Goerli] added `goerli.beacon.proposer_slashings`, `goerli.beacon.recent_proposer_slashings`
* \[Goerli] added `goerli.beacon.bls_to_execution_changes`, `goerli.beacon.recent_bls_to_execution_changes`
* \[Goerli] added `goerli.beacon.withdrawals`, `goerli.beacon.recent_withdrawals`
* \[Beacon] added [Goerli Beacon HTTP API](https://docs.spice.xyz/api/goerli/beacon-http-api)
* \[Beacon] added [Ethereum Beacon HTTP API ](https://docs.spice.xyz/api/ethereum/beacon-http-api)
* \[Aave V2] added `eth.aave_v2.loan_updates`, `eth.aave_v2.loans`
* \[Aave V2] added `eth.aave_v2.collateral_updates`, `eth.aave_v2.collateral`
* \[Chainlink] added `eth.chainlink.prices`, `eth.chainlink.recent_prices`
* \[Chainlink] added [price feed support](/broken/pages/wHEvZjBoobfKzFCNaX0E) for the following assets:
  * USD Coin (USDC)&#x20;
  * 1INCH Token (1INCH)&#x20;
  * Yearn.finance (YFI)&#x20;
  * Dai Stablecoin (DAI)&#x20;
  * DefiPulse Index (DPI)&#x20;
  * UST (UST)&#x20;
  * Decentraland MANA (MANA)&#x20;
  * Liquid staked Ether 2.0 (stETH)&#x20;
  * ChainLink Token (LINK)&#x20;
  * Balancer (BAL)&#x20;
  * Enjin Coin (ENJ)&#x20;
  * Tether USD (USDT)&#x20;
  * Wrapped BTC (WBTC)&#x20;
  * Maker (MKR)&#x20;
  * Convex Token (CVX)&#x20;
  * Fei USD (FEI)&#x20;
  * Uniswap (UNI)&#x20;
  * Pax Dollar (USDP)&#x20;
  * Kyber Network Crystal (KNC)&#x20;
  * Aave Token (AAVE)&#x20;
  * 0x Protocol Token (ZRX)&#x20;
  * Binance USD (BUSD)&#x20;
  * SushiBar (xSUSHI)&#x20;
  * Republic Token (REN)&#x20;
  * renFIL (renFIL)&#x20;
  * Basic Attention Token (BAT)&#x20;
  * Synthetix Network Token (SNX)&#x20;
  * Ampleforth (AMPL)&#x20;
  * Frax (FRAX)&#x20;
  * Rai Reflex Index (RAI)&#x20;
  * Synth sUSD (sUSD)&#x20;
  * TrueUSD (TUSD) <br>

#### Changes

* \[Portal] Improved performance of portal pages
* \[Performance] Improved query performance, data redundancy, and scale by migrating to new infrastructure

#### Resources

* [Getting started with Spice AI](https://docs.spice.xyz/get-started)
* [Documentation](https://docs.spice.xyz/)
* [FAQ](https://docs.spice.xyz/faq)

{% endupdate %}

{% update date="2023-03-31" %}
## March 2023

If you are waitlisted, you can now instantly enable your account by signing up for a preview plan at 50% off. This month's release also includes support for the Ethereum Beacon chain dataset and improved monitoring with request logs.

**Self-Service Activation from Waitlist:** as a waitlisted customer, you can now enable your account instantly without manual Spice AI approval when you sign up for a Pro for Teams or Lite Plan. Begin a 7-day trial of the Pro for Team plan, then monthly subscription of $1,245 at 50% off (normally $2,490). It comes with early access (off the waitlist), dedicated support on Discord, and a bi-weekly sync with the Spice AI engineering team.

**Improved monitoring:** you can now track requests, their status code, and duration to Spice, in addition to the existing usage monitoring metrics dashboard. Start by going to your app, under the Monitor sidebar, select API Requests. You can then toggle between Metrics and Logs views. Within Logs, you can select to retrieve API requests from the past 1 hour, 8 hours, 24 hours, and up to the past 3 days.

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

**Ethereum Beacon Chain Dataset (Preview):** The Ethereum beacon chain is the central coordination and consensus layer for the Ethereum 2.0 upgrade, which aims to improve scalability, security, and sustainability. Now you can query and access beacon chain dataset with `eth.beacon`, `eth.beacon.validators`, `eth.beacon.withdrawals` and more.

You can also reference the full query list via [Ethereum Beacon tables](https://docs.spice.xyz/reference/sql-query-tables/sql-query-tables/beacon-chain-tables) or by directly querying the following in the application:

```sql
SHOW TABLES IN eth.beacon
```

[Example SQL queries](https://docs.spice.xyz/reference/example-ethereum-beacon-sql-queries) include slots, attestations, validators, deposits, voluntary exits, attester slashings and more, all retrievable within a few seconds.&#x20;

Sample Queries

1.  ENS Domain Names that map to a validator<br>

    <figure><img src="../.gitbook/assets/Screen Shot 2023-04-24 at 10.59.20 PM (1).png" alt=""><figcaption></figcaption></figure>
2.  Number of Weekly Attester Slashings <br>

    <figure><img src="../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>
3.  Top 10 most frequent block proposers<br>

    <figure><img src="../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

By leveraging beacon chain capabilities, developers can create scalable decentralized applications that can handle a large volume of transactions, while also participating in network governance and incentivization through its Proof of Stake consensus mechanism. You can learn more about the chain [here](https://www.alchemy.com/overviews/what-is-the-ethereum-beacon-chain).&#x20;

#### New In this Release

* \[Portal] Added Self-Service account activation
* \[Beacon] Added Ethereum beacon chain dataset `eth.beacon`
* Beacon] Added Ethereum beacon chain dataset `eth.validators`
* \[Beacon] Added Ethereum beacon chain datasets `eth.beacon.slots`, `eth.beacon.recent_slots`
* \[Beacon] Added Ethereum beacon chain datasets `eth.beacon.attestations`, `eth.beacon.recent_attestations`
* \[Beacon] Added Ethereum beacon chain datasets `eth.beacon.deposits`, `eth.beacon.recent_deposits`
* \[Beacon] Added Ethereum beacon chain datasets `eth.beacon.voluntary_exits`, `eth.beacon.recent_voluntary_exits`
* \[Beacon] Added Ethereum beacon chain datasets `eth.beacon.attester_slashings`, `eth.beacon.recent_attester_slashings`
* \[Beacon] Added Ethereum beacon chain datasets `eth.beacon.proposer_slashings`, `eth.beacon.recent_proposer_slashings`
* \[Beacon] Added Ethereum beacon chain datasets `eth.beacon.bls_to_execution_changes`, `eth.beacon.recent_bls_to_execution_changes`
* \[Beacon] Added Ethereum beacon chain datasets `eth.beacon.withdrawals`, `eth.beacon.recent_withdrawals`

#### Changes

* \[Portal] Improved performance of portal pages

#### Resources

* [Getting started with Spice AI](https://docs.spice.xyz/get-started)
* [Documentation](https://docs.spice.xyz/)
* [FAQ](https://docs.spice.xyz/faq)

{% endupdate %}

{% update date="2023-02-28" %}
## February 2023

Spice released the NFT dataset with Metadata in preview, along with the Historic Prices API in preview, and the v0.3 update to the Node.js SDK [spice.js](https://www.npmjs.com/package/@spiceai/spice).&#x20;

**Historic Prices API (Preview):** In addition to accessing the [Spot Prices](https://docs.spice.xyz/api/prices#spot-prices) of popular token/currency pairs on top exchanges, users can now access the **`/v0.1/prices/[pair]`** API, which returns historical prices of the specified token/currency pair. You can refine your search by defining parameters like `start` and `end`, which will return all prices between the two timestamps at a provided `granularity`.

Please contact us if you would like to access the API. \
\
**NFT Dataset with Metadata (Preview):** You can now query and leverage fungible and non-fungible token contracts under `eth.tokens` as well as isolated NFT specific per token information under `eth.nfts`. These are made available also with name and symbol for ERC721 tokens. See the full list of [NFT tables](https://docs.spice.xyz/reference/sql-query-tables/sql-query-tables/nft-tables) and [Token tables](https://docs.spice.xyz/reference/sql-query-tables/sql-query-tables/token-tables).&#x20;

**Node.js SDK v0.3:** adds support for improved [Prices APIs](https://docs.spice.xyz/api/prices) supporting spot/latest and historical prices for pair across Coinbase, Gemini, and tracked by Coinmarket Cap. This upgrade ensures the SDK remains the easiest way to use and query [Spice.xyz](https://spice.xyz) with Node.js.

It uses [Apache Apache Flight](https://arrow.apache.org/docs/format/Flight.html) to efficiently stream data to the client and [Apache Arrow](https://arrow.apache.org/) Records as data frames which are then easily converted to JavaScript objects/arrays or JSON.

New functions available on the client are:

* `getPrice()`
* `getPrices()`

You'll need [Node.js 16+](https://nodejs.org/) to get started. See the step-by-step installation guide [here](https://docs.spice.xyz/sdks/node.js-sdk).&#x20;

#### New In this Release

* \[API] Added [Historic Prices API in Preview](https://docs.spice.xyz/api/prices#historical-prices-preview)
* \[Node.js SDK] [v0.3 release](https://docs.spice.xyz/sdks/node.js-sdk) of Node.js SDK [spice.js](https://www.npmjs.com/package/@spiceai/spice)
* \[NFT and Metadata] [NFT](https://docs.spice.xyz/reference/sql-query-tables/sql-query-tables/nft-tables) and [Token](https://docs.spice.xyz/reference/sql-query-tables/sql-query-tables/token-tables) datasets in preview&#x20;

#### Changes

* \[Performance] Improved query performance and scale
* \[Portal] Improved performance of portal pages
* \[Node.js SDK] Add `getPrice` and `getPrices` API&#x20;

#### Resources

* [Getting started with Spice AI](https://docs.spice.xyz/get-started)
* [Documentation](https://docs.spice.xyz/)
* [FAQ](https://docs.spice.xyz/faq)

{% endupdate %}

{% update date="2023-01-31" %}
## January 2023

Sharing a packed release this month focused on a new dataset, org management, two developer querying tools, and two SDKs.

**Ethereum Name Service (ENS) Events:** now out of preview, it's available by querying `ens.domains`, which is updated in real-time to have the latest `eth_address`whenever a transfer or name-registered event happens. Both `eth.ens` and other on-demand events indexed under the `eth.ens` prefix such as `eth.ens_event_nameregistered` are updated in real time with the same high-performance standards of published Spice datasets. See the tables [here](https://docs.spice.xyz/reference/sql-query-tables/sql-query-tables/token-tables-1).

**Organization Management:** existing users can start exploring org support and management for users, apps, and datasets by [authorizing Spice with read-only access](https://docs.spice.xyz/get-started/portal-login) when logging into the [Spice.xyz](http://spice.xyz/) portal. This will also act as a parent container for future concepts including apps, users, teams, datasets, and metrics for streamlined management and navigation within [spice.xyz](http://spice.xyz/).

The big game changers this month are [Async Query](https://docs.spice.xyz/api/sql-query-api/http-api-1) and [Flow AI Query Auto-Complete (in preview)](https://medium.com/spice-ai/introducing-spice-ai-flow-8bcfc5cc2294) — which supercharge the developer experience.

**Async Query API:** you can post a JSON payload specifying list of query completion, and  `notifications` results can be fetched asynchronously once the query has completed. Choose function client apps such as AWS Lambda to async query Spice.xyz and receive result notifications by webhook. Results are accessible up to 20 minutes after they are posted.

**Flow (AI SQL Query Auto-Complete):** you can now query with plain text, hit enter, and watch as Flow autocompletes the request with the corresponding SQL lines in your [spice.xyz](http://spice.xyz/) query editor. Hit tab to accept the AI-generated completion, then accept, reject, or edit before run the query.

We've also released the new [gospice Golang SDK](https://docs.spice.xyz/sdks/go) and added improvements to our [Python SDK](https://docs.spice.xyz/sdks/python-sdk) including timeout support.

**Go SDK:** gospice is the easiest way to query [Spice.xyz](https://spice.xyz/) from Go. It uses [Apache Apache Flight](https://arrow.apache.org/docs/format/Flight.html) to efficiently stream data to the client and [Apache Arrow](https://arrow.apache.org/) Records as data frames.\
GoDocs available at [pkg.go.dev/github.com/spiceai/gospice](https://pkg.go.dev/github.com/spiceai/gospice).

We continue to make performance improvements for faster, more reliable queries and also recently launched our much anticipated Polygon Support, which you can read more about [here](https://blog.spice.ai/build-multi-chain-with-spice-ai-now-with-polygon-support-9db156f36d80).

Read the announcement post at [blog.spice.ai](https://blog.spice.ai/spice-xyz-january-update-whats-new-dd50f2233f47)

#### New In this Release

* \[Portal] Added [organizations support and management](https://docs.spice.xyz/portal/organizations#organization-management)
* \[Portal] Added [AI-driven SQL Query Auto-Complete](https://docs.spice.xyz/portal/query-editor#ai-sql-completion-preview)
* \[API] Added [Async Query API](https://docs.spice.xyz/api/sql-query-api/http-api-1)
* \[ENS] General availability of [ENS data](https://docs.spice.xyz/reference/sql-query-tables/sql-query-tables/token-tables-1) out of preview
* \[Go SDK] Initial release of [Golang SDK](https://docs.spice.xyz/sdks/go)

#### Changes

* \[Performance] Improved query performance and scale
* \[Portal] Improved performance of portal pages
* \[Polygon] Added [WebSocket support](https://docs.spice.xyz/faq#do-you-support-websockets-or-other-streaming) for Polygon
* \[Python SDK] Added support for Python 3.11
* \[Python SDK] Added back support for Python 3.7
* \[Python SDK] Added query timeout support
* \[Python SDK] Upgraded to pyarrow 10.0.1
* \[Node.js SDK] Added Async query support
* \[Node.js SDK] Upgraded to arrow 10.0.1

#### Resources

* [Getting started with Spice AI](https://docs.spice.xyz/get-started)
* [Documentation](https://docs.spice.xyz/)
* [FAQ](https://docs.spice.xyz/faq)

{% endupdate %}

{% update date="2022-12-31" %}
## December 2022

Spice supports querying [Polygon data](/broken/pages/iyngGYSqtXIiSELjLtA5) in addition to the existing [Ethereum](/broken/pages/nLS2MJKcNRnMbanBVJUv) and [BItcoin](/broken/pages/TGHKku5PSEIlCSApa727) datasets.

Read more on the [Spice AI Blog](https://blog.spice.ai/build-multi-chain-with-spice-ai-now-with-polygon-support-9db156f36d80).

**Changes**

* Support for fundamental Polygon datasets and NFT specific tables&#x20;
  * `polygon.blocks` / `polygon.recent_blocks`
  * `polygon.transactions` / `polygon.recent_transactions`
  * `polygon.nft_transfers` /`polygon.recent_nft_transfers`&#x20;
  * `polygon.nft_airdrop_transfers` / `polygon.recent_nft_airdrop_transfers`
  * `polygon.nft_owners`
* View the columns and their schema available for each table with the `describe <table>` command&#x20;

#### Resources

* [Getting started with Spice AI](https://docs.spice.xyz/get-started)
* [Documentation](https://docs.spice.xyz/)
* [FAQ](https://docs.spice.xyz/faq)

{% endupdate %}

{% update date="2022-11-30" %}
## November 2022

**DEX Liquidity & Events Dataset**

Spice now features DEX Liquidity and Events Dataset where users can access indexed liquidity data from UniSwap \[[V2](/broken/pages/uaSlGYUUtkb2FJZh6p9d), [V3](/broken/pages/mIc6VZGqHQoS7EhH1W3b)] and [SushiSwap](/broken/pages/DeA1ZPzU5c1VZzyQouRF) liquidity pools, _within seconds_.&#x20;

Data is available both historically and in real time, providing unparalleled visibility on DEX liquidity pools.

**ENS (Preview)**\
You can now preview the new dataset for [Ethereum Name Service events](/broken/pages/BPicAdfXhg6ZdvP21FJ5), available by querying `eth.ens`. Within the dataset, you’ll find that each ENS event will have its own table as immutable data.

This also includes the new `ens.domains` dataset, which is updated in real-time to have the latest `eth_address` whenever a transfer or name-registered event happens

Read more on the [Spice AI blog](https://blog.spice.ai/whats-new-b7f637897df8).

**Changes**

* [Prices dataset](/broken/pages/zXpdOConoLcf9VN61mY5) now out of preview
* Added export to CSV results format in addition to existing formats [JSON](/broken/pages/w4hRmvPIKXDjLDknyEl6) and [Apache Arrow](../api/sql-query/apache-arrow-flight-api.md), for easy use with apps, ML, or libraries like NumPy and Pandas
* Added performance improvements for faster queries across existing datasets including `eth.traces`&#x20;

#### Resources

* [Getting started with Spice AI](https://docs.spice.xyz/get-started)
* [Documentation](https://docs.spice.xyz/)
* [FAQ](https://docs.spice.xyz/faq)

{% endupdate %}

{% update date="2022-10-31" %}
## October 2022

Spice now has [prices dataset](/broken/pages/zXpdOConoLcf9VN61mY5) in preview.&#x20;

* Query historical High-Low-Open-Close (HILO) prices to minute precision
* Join with other real-time and historical web3 data to do calculations and conversions back to USD
* Initial data sources include Gemini, Coinbase, SushiSwap, and UniSwap (a mix of CEX, DEX, and third parties)

Read more on the [Spice AI blog](https://blog.spice.ai/whats-new-b7f637897df8).

#### Resources

* [Getting started with Spice AI](https://docs.spice.xyz/get-started)
* [Documentation](https://docs.spice.xyz/)
* [FAQ](https://docs.spice.xyz/faq)

{% endupdate %}

{% update date="2022-09-30" %}
## September 2022

**Ethereum Traces Dataset**

Spice launches the `eth.traces` dataset for both Ethereum and Polygon. The Ethereum internal transactions are available for query at `eth.traces` and are updated in real-time.



**Polygon Support (Preview)**

Spice launches and tests [Polygon support](/broken/pages/iyngGYSqtXIiSELjLtA5).

**Changes**

* completed real-time indexing and parquet writing for Polygon
* incorporated asynchronous process block notifications to enable Polygon support
* added improvements on data integrity and bug fixes
* added transaction\_hash and block\_timestamp to `eth.nft_owners`

#### Resources

* [Getting started with Spice AI](https://docs.spice.xyz/get-started)
* [Documentation](https://docs.spice.xyz/)
* [FAQ](https://docs.spice.xyz/faq)

{% endupdate %}

{% update date="2022-08-31" %}
## August 2022

Key infrastructure improvements were made for Spice's query performance, security, and data availability.

**Changes**

* Improved performance for node configuration
* Upgrade dremio infrastructure
* Expanded node memory for broader availability

#### Resources

* [Getting started with Spice AI](https://docs.spice.xyz/get-started)
* [Documentation](https://docs.spice.xyz/)
* [FAQ](https://docs.spice.xyz/faq)

{% endupdate %}

{% update date="2022-07-31" %}
## July 2022

Spice supports querying [Bitcoin data](/broken/pages/TGHKku5PSEIlCSApa727) in addition to the existing [Ethereum datasets](/broken/pages/nLS2MJKcNRnMbanBVJUv).

Read more on the [Spice AI blog](https://medium.com/spice-ai/).

**Changes**

* Support for fundamental Bitcoin datasets
  * `btc.blocks` / `btc.recent_blocks`
  * `btc.transactions` / `btc.recent_transactions`
  * `btc.transaction_inputs` / `btc.recent_transaction_inputs`
  * `btc.transaction_outputs` / `btc.recent_transaction_outputs`
* [Broken link](/broken/pages/YphzWK8SmAbgWoDBCGMW "mention") for querying Bitcoin data.

#### Resources

* [Getting started with Spice AI](https://docs.spice.xyz/get-started)
* [Documentation](https://docs.spice.xyz/)
* [FAQ](https://docs.spice.xyz/faq)

{% endupdate %}

{% update date="2022-06-30" %}
## June 2022

This update includes a new [Javascript/Typescript SDK](../sdks/node.js-sdk/) for Node.js, an improved [Python SDK](../sdks/python-sdk/), and performance improvements for Ethereum tokens and NFT datasets.

[Ethereum Name Service (ENS)](/broken/pages/BPicAdfXhg6ZdvP21FJ5) support is also available in beta with the new `ens.domains` dataset.

Read more on the [Spice AI blog](https://medium.com/spice-ai/spice-xyz-june-update-f74d60faff61).

**Changes**

* New [Node.js SDK](../sdks/node.js-sdk/)
* Improved [Python SDK](../sdks/python-sdk/)
* ENS support in beta
  * `ens.domains`
* Improved performance for `eth.tokens_` and `eth.nft_` prefixed tables

#### Resources

* [Getting started with Spice AI](https://docs.spice.xyz/get-started)
* [Documentation](https://docs.spice.xyz/)
* [FAQ](https://docs.spice.xyz/faq)

{% endupdate %}

{% update date="2022-05-31" %}
## May 2022

Spice now has some of the best automated token standard detection for erc20, erc721, and erc1155 tokens available. Tokens are detected by their signatures and as they emit events over time, so Spice provides a probability of standards compliance to each contract. Along with this support are now token-specific tables like `eth.tokens_erc1155` and `eth.token_transfers_erc20`. See the entire list [Tokens Tables](/broken/pages/WJRmqmAyo4hX6F5tRL2d).

Performance is now even better, especially for larger queries, and for results over the HTTP API. We still recommend the [Apache Arrow Flight API](../api/sql-query/apache-arrow-flight-api.md) that's easily accessible via the [Python SDK](../sdks/python-sdk/) for production use though.

WebSocket support is now available in private preview to Design Partners - get in touch if you are interested in custom limits, early access features, and dedicated support.

#### Changes

* Improved Ethereum standard detection for erc20, erc721, and erc115 tokens
* Added new token specific tables
  * `eth.tokens_erc20`
  * `eth.tokens_erc721`
  * `eth.tokens_erc1155`
  * `eth.token_transfers_erc20`
  * `eth.token_transfers_erc721`
  * `eth.token_transfers_erc1155`
* Added new columns to `eth.token_transfers` table
  * `token_standard`
  * `token_id`
* Added new columns to `eth.contracts` table
  * `erc20_confidence`
  * `erc721_confidence`
  * `erc1155_confidence`
  * `is_erc1155`
* `eth.nft_` tables now include erc1155 tokens

#### Resources

* [Getting started with Spice AI](https://docs.spice.xyz/get-started)
* [Documentation](https://docs.spice.xyz/)
* [FAQ](https://docs.spice.xyz/faq)

{% endupdate %}

{% update date="2022-04-30" %}
## April 2022

Spice is now hosted at [**spice.xyz**](https://spice.xyz)! And with this release Spice is now **100% data complete**! We also have new **combined documentation** at [docs.spice.xyz](https://docs.spice.xyz), new **NFT specific tables** like `nft_transfers` and `nft_airdrop_transfers`, significantly **improved erc721 detection**, and **higher data integrity**.

#### Changes

* Now hosted at [spice.xyz](https://spice.xyz)
* Now 100% data complete on core Ethereum tables
* Refreshed documentation site at [docs.spice.xyz](https://docs.spice.xyz)
* Added `input` field to the `transactions` table
* Added `data`, `topics`, `block_timestamp` fields to the `logs` table
* Added `block_timestamp` and `block_hash` to the `token_transfers` table

#### Breaking Changes

* The receipts table fields have been moved to the transactions table with prefix receipt\_. The told receipts table has been removed.
* `receipts.cumulative_gas_used` → `transactions.receipt_cumulative_gas_used`
* `receipts.gas_used` → `transactions.receipt_gas_used`
* `receipts.contract_address` → `transactions.receipt_contract_address`
* `receipts.root` → `transactions.receipt_root`
* `receipts.effective_gas_price` → `transactions.receipt_effective_gas_price`

#### Resources

* [Getting started with Spice AI](https://docs.spice.xyz/get-started)
* [Documentation](https://docs.spice.xyz/)
* [FAQ](https://docs.spice.xyz/faq)

{% endupdate %}

{% update date="2022-03-31" %}
## March 2022

More improvements to performance and data completeness. Now 99.99% complete for Ethereum base types.

Significantly improved erc721 detection and now include new `nft` specific tables including `eth.nft_contracts`, `eth.nft_transfers` and `eth.nft_airdrop_transfers`.

#### Changes

* Improved erc721 detection, now with 28,000+ erc721 rows
* Adds `eth.nft_contracts` table
* Adds `eth.nft_transfers` table
* Adds `eth.nft_airdrop_transfers` table
* Adds `eth.recent_blocks` table which holds the latest 100 blocks
* More improvements to query performance
* Now at 99.99% data complete

#### Resources

* [Getting started with Spice AI](https://docs.spice.xyz/get-started)
* [Documentation](https://docs.spice.xyz/)
* [FAQ](https://docs.spice.xyz/faq)

{% endupdate %}

{% update date="2022-02-28" %}
## February 2022

Significant upgrades in performance and data completeness. Adds the ability to query Ethereum, Sushiswap, and Uniswap data with SQL. Adds an Apache Flight endpoint.

#### Changes

* Adds SQL query
* Adds Sushiswap, Uniswap-V2, and Uniswap-V3 liquidity data
* Adds Apache Flight endpoint
* Adds management portal
* Significantly improved performance with the ability to query across real-time and historical data

#### Resources

* [Getting started with Spice AI](https://docs.spice.xyz/get-started)
* [Documentation](https://docs.spice.xyz/)
* [FAQ](https://docs.spice.xyz/faq)

{% endupdate %}

{% update date="2022-01-31" %}
## January 2022

Initial release of the Spice beta!

#### Changes

* The Initial release of the Spice Private Beta!
* Adds `/eth/v0.1/gasfees` API.
* Adds `/eth/v0.1/contracts` API.
* Adds `/v0.1/prices` API.

{% endupdate %}

{% endupdates %}
