---
description: Scrape per-project runtime metrics
icon: chart-line
---

# Metrics API

The metrics endpoint exposes per Spice project instance runtime metrics in Prometheus format. Use this endpoint to scrape metrics and send them to Datadog, Grafana, Prometheus, and OpenTelemetry compatible systems for dashboards and alerting.

{% hint style="info" %}
**Runtime Endpoint:** The metrics endpoint is served by your Spice runtime instance (e.g. `https://<project-cname>.spiceai.io`), not the Management API (`https://api.spice.ai`). Authenticate with your **project API key**, not a Personal Access Token.
{% endhint %}

## Get Metrics

<mark style="color:blue;">`GET`</mark> `https://<project-cname>.spiceai.io/v1/metrics`

Returns runtime metrics for the Spice project instance in Prometheus exposition format.

### Headers

| Header      | Type   | Description      |
| ----------- | ------ | ---------------- |
| `X-API-Key` | string | Your project API key |

### Response

{% tabs %}
{% tab title="200: OK" %}
Returns metrics in Prometheus text exposition format.

```
# HELP http_requests Number of HTTP requests.
# TYPE http_requests counter
http_requests_total{method="GET",path="/v1/sql"} 1024

# HELP query_duration_ms The total amount of time spent planning and executing queries in milliseconds.
# TYPE query_duration_ms histogram
query_duration_ms_bucket{le="10"} 50
query_duration_ms_bucket{le="100"} 200
...
```
{% endtab %}

{% tab title="401: Unauthorized" %}
```json
{
  "error": "Unauthorized"
}
```
{% endtab %}
{% endtabs %}

### Example

```bash
curl https://us-west-2-prod-aws-data.spiceai.io/v1/metrics \
  -H "X-API-Key: <API_KEY>"
```

## Monitoring Integrations

The metrics output is compatible with standard monitoring systems. Pre-built dashboards and setup guides are available:

* [Grafana & Prometheus](../monitoring/grafana.md) - Scrape metrics with Prometheus, visualize in Grafana
* [Datadog](../monitoring/datadog.md) - Scrape metrics with the Datadog Agent's OpenMetrics integration
* **OpenTelemetry** - Use the OTEL Prometheus receiver

{% hint style="warning" %}
The metrics endpoint provides Spice runtime metrics only. Kubernetes pod-level metrics (CPU, memory, etc.) are not currently included.
{% endhint %}

## Available Metrics

All metrics include relevant labels (dimensions) for filtering and aggregation.

### Acceleration

| Metric                                                 | Type      | Description                                                                                                                                   |
| ------------------------------------------------------ | --------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| `accelerated_ready_state_federated_fallback`           | count     | Number of times the federated table was queried due to the accelerated table loading the initial data.                                        |
| `accelerated_zero_results_federated_fallback`          | count     | Number of times the federated table was queried due to the accelerated table returning zero results.                                          |
| `dataset_acceleration_ingestion_lag_ms`                | gauge     | Lag between the current wall-clock time and the maximum time\_column value after the refresh operation, in milliseconds. Disabled by default. |
| `dataset_acceleration_last_refresh_time_ms`            | gauge     | Unix timestamp in milliseconds when the last refresh completed. Disabled by default.                                                          |
| `dataset_acceleration_max_timestamp_after_refresh_ms`  | gauge     | Maximum value of the dataset's time\_column after the refresh operation, in milliseconds. Disabled by default.                                |
| `dataset_acceleration_max_timestamp_before_refresh_ms` | gauge     | Maximum value of the dataset's time\_column before the refresh operation, in milliseconds. Disabled by default.                               |
| `dataset_acceleration_refresh_data_fetches_skipped`    | count     | Number of refresh data fetches skipped due to unchanged file metadata.                                                                        |
| `dataset_acceleration_refresh_duration_ms`             | histogram | Duration in milliseconds to load a full or appended refresh data.                                                                             |
| `dataset_acceleration_refresh_errors`                  | count     | Number of errors refreshing the dataset.                                                                                                      |
| `dataset_acceleration_refresh_lag_ms`                  | gauge     | Difference between the maximum time\_column value after and before the refresh operation, in milliseconds.                                    |
| `dataset_acceleration_refresh_worker_panics`           | count     | Number of times a refresh worker panicked while refreshing a dataset.                                                                         |
| `dataset_acceleration_snapshot_bootstrap_bytes`        | gauge     | Number of bytes downloaded when bootstrapping the acceleration from a snapshot.                                                               |
| `dataset_acceleration_snapshot_bootstrap_checksum`     | gauge     | Checksum of the snapshot downloaded during bootstrap (emitted with `checksum` attribute).                                                     |
| `dataset_acceleration_snapshot_bootstrap_duration_ms`  | count     | Time in milliseconds taken to download the snapshot used to bootstrap acceleration.                                                           |
| `dataset_acceleration_snapshot_failure_count`          | count     | Number of failures encountered while writing snapshots.                                                                                       |
| `dataset_acceleration_snapshot_write_bytes`            | gauge     | Number of bytes written for the most recent snapshot.                                                                                         |
| `dataset_acceleration_snapshot_write_checksum`         | gauge     | Checksum of the most recent snapshot write (emitted with `checksum` attribute).                                                               |
| `dataset_acceleration_snapshot_write_duration_ms`      | histogram | Time in milliseconds taken to write the latest snapshot to object storage.                                                                    |
| `dataset_acceleration_snapshot_write_timestamp`        | gauge     | Unix timestamp (seconds) when the most recent snapshot write completed.                                                                       |

### Datasets

| Metric                        | Type  | Description                                                                                        |
| ----------------------------- | ----- | -------------------------------------------------------------------------------------------------- |
| `dataset_active_count`        | gauge | Number of currently loaded datasets.                                                               |
| `dataset_load_errors`         | count | Number of errors loading the dataset.                                                              |
| `dataset_load_state`          | gauge | Status of the dataset. 0=Initializing, 1=Ready, 2=Disabled, 3=Error, 4=Refreshing, 5=ShuttingDown. |
| `dataset_unavailable_time_ms` | gauge | Time dataset went offline in milliseconds.                                                         |

### Catalogs

| Metric                | Type  | Description                                                                                                 |
| --------------------- | ----- | ----------------------------------------------------------------------------------------------------------- |
| `catalog_load_errors` | count | Number of errors loading the catalog provider.                                                              |
| `catalog_load_state`  | gauge | Status of the catalog provider. 0=Initializing, 1=Ready, 2=Disabled, 3=Error, 4=Refreshing, 5=ShuttingDown. |

### Queries

| Metric                        | Type      | Description                                                                                                                                     |
| ----------------------------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| `query_active_count`          | histogram | Number of concurrent top-level queries actively being processed. Includes the `protocol` dimension (`http`, `flight`, `flightsql`, `internal`). |
| `query_duration_ms`           | histogram | The total amount of time spent planning and executing queries in milliseconds.                                                                  |
| `query_execution_duration_ms` | histogram | The total amount of time spent only executing queries (0 for cached queries).                                                                   |
| `query_executions`            | count     | Number of query executions.                                                                                                                     |
| `query_failures`              | count     | Number of query failures.                                                                                                                       |
| `query_processed_bytes`       | count     | Number of bytes processed by the runtime.                                                                                                       |
| `query_produced_spills`       | count     | Number of spills produced by the query.                                                                                                         |
| `query_returned_bytes`        | count     | Number of bytes returned to query clients.                                                                                                      |
| `query_returned_rows`         | histogram | Number of rows returned to query clients.                                                                                                       |
| `query_spilled_bytes`         | count     | Number of spilled bytes produced by the query.                                                                                                  |
| `query_spilled_rows`          | count     | Number of spilled rows produced by the query.                                                                                                   |

### HTTP & Flight

| Metric                                 | Type      | Description                                               |
| -------------------------------------- | --------- | --------------------------------------------------------- |
| `http_requests`                        | count     | Number of HTTP requests.                                  |
| `http_requests_duration_ms`            | histogram | Measures the duration of HTTP requests in milliseconds.   |
| `flight_requests`                      | count     | Total number of Flight requests.                          |
| `flight_request_duration_ms`           | histogram | Measures the duration of Flight requests in milliseconds. |
| `flight_do_exchange_data_updates_sent` | count     | Number of data updates sent via DoExchange.               |

### AI / LLM

| Metric                             | Type      | Description                                                                                          |
| ---------------------------------- | --------- | ---------------------------------------------------------------------------------------------------- |
| `ai_inferences_with_spice_count`   | count     | AI Inferences with Spice count.                                                                      |
| `llm_requests`                     | count     | Number of LLM requests.                                                                              |
| `llm_failures`                     | count     | Number of LLM failures.                                                                              |
| `llm_internal_request_duration_ms` | histogram | The duration of running an LLM request internally.                                                   |
| `llm_load_state`                   | gauge     | Status of the LLM model. 0=Initializing, 1=Ready, 2=Disabled, 3=Error, 4=Refreshing, 5=ShuttingDown. |

### Models

| Metric                   | Type      | Description                                                                                      |
| ------------------------ | --------- | ------------------------------------------------------------------------------------------------ |
| `model_active_count`     | gauge     | Number of currently loaded models.                                                               |
| `model_load_duration_ms` | histogram | Duration in milliseconds to load the model.                                                      |
| `model_load_errors`      | count     | Number of errors loading the model.                                                              |
| `model_load_state`       | gauge     | Status of the model. 0=Initializing, 1=Ready, 2=Disabled, 3=Error, 4=Refreshing, 5=ShuttingDown. |

### Embeddings

| Metric                                    | Type      | Description                                                                                          |
| ----------------------------------------- | --------- | ---------------------------------------------------------------------------------------------------- |
| `embeddings_active_count`                 | gauge     | Number of currently loaded embeddings.                                                               |
| `embeddings_requests`                     | count     | Number of embedding requests.                                                                        |
| `embeddings_failures`                     | count     | Number of embedding failures.                                                                        |
| `embeddings_internal_request_duration_ms` | histogram | The duration of running an embedding(s) internally.                                                  |
| `embeddings_load_errors`                  | count     | Number of errors loading the embedding.                                                              |
| `embeddings_load_state`                   | gauge     | Status of the embedding. 0=Initializing, 1=Ready, 2=Disabled, 3=Error, 4=Refreshing, 5=ShuttingDown. |

### Embeddings Cache

| Metric                                        | Type  | Description                                                                                           |
| --------------------------------------------- | ----- | ----------------------------------------------------------------------------------------------------- |
| `embeddings_cache_hits`                       | count | Cache hit count.                                                                                      |
| `embeddings_cache_misses`                     | count | Cache miss count.                                                                                     |
| `embeddings_cache_requests`                   | count | Number of requests to get a key from the cache.                                                       |
| `embeddings_cache_evictions`                  | count | Number of cache evictions.                                                                            |
| `embeddings_cache_hit_ratio`                  | gauge | Cache hit ratio (hits / total requests).                                                              |
| `embeddings_cache_items_count`                | gauge | Number of items currently in the cache.                                                               |
| `embeddings_cache_size_bytes`                 | gauge | Size of the cache in bytes.                                                                           |
| `embeddings_cache_max_size_bytes`             | gauge | Maximum allowed size of the cache in bytes.                                                           |
| `embeddings_cache_stale_swr_count`            | count | Number of stale-while-revalidate background refreshes skipped due to existing in-flight revalidation. |
| `embeddings_cache_swr_background_query_count` | count | Number of background queries triggered for stale-while-revalidate cache refreshes.                    |

### Results Cache

| Metric                                     | Type  | Description                                                                                           |
| ------------------------------------------ | ----- | ----------------------------------------------------------------------------------------------------- |
| `results_cache_hits`                       | count | Cache hit count.                                                                                      |
| `results_cache_misses`                     | count | Cache miss count.                                                                                     |
| `results_cache_requests`                   | count | Number of requests to get a key from the cache.                                                       |
| `results_cache_evictions`                  | count | Number of cache evictions.                                                                            |
| `results_cache_hit_ratio`                  | gauge | Cache hit ratio (hits / total requests).                                                              |
| `results_cache_items_count`                | gauge | Number of items currently in the cache.                                                               |
| `results_cache_size_bytes`                 | gauge | Size of the cache in bytes.                                                                           |
| `results_cache_max_size_bytes`             | gauge | Maximum allowed size of the cache in bytes.                                                           |
| `results_cache_stale_swr_count`            | count | Number of stale-while-revalidate background refreshes skipped due to existing in-flight revalidation. |
| `results_cache_swr_background_query_count` | count | Number of background queries triggered for stale-while-revalidate cache refreshes.                    |

### Search Results Cache

| Metric                                            | Type  | Description                                                                                           |
| ------------------------------------------------- | ----- | ----------------------------------------------------------------------------------------------------- |
| `search_results_cache_hits`                       | count | Search cache hit count.                                                                               |
| `search_results_cache_misses`                     | count | Cache miss count.                                                                                     |
| `search_results_cache_requests`                   | count | Number of requests to get a key from the search cache.                                                |
| `search_results_cache_evictions`                  | count | Number of cache evictions.                                                                            |
| `search_results_cache_hit_ratio`                  | gauge | Cache hit ratio (hits / total requests).                                                              |
| `search_results_cache_items_count`                | gauge | Number of items currently in the search cache.                                                        |
| `search_results_cache_size_bytes`                 | gauge | Size of the search cache in bytes.                                                                    |
| `search_results_cache_max_size_bytes`             | gauge | Maximum allowed size of the search cache in bytes.                                                    |
| `search_results_cache_stale_swr_count`            | count | Number of stale-while-revalidate background refreshes skipped due to existing in-flight revalidation. |
| `search_results_cache_swr_background_query_count` | count | Number of background queries triggered for stale-while-revalidate cache refreshes.                    |

### Tools & Views

| Metric              | Type  | Description                                                                                          |
| ------------------- | ----- | ---------------------------------------------------------------------------------------------------- |
| `tool_active_count` | gauge | Number of currently loaded LLM tools.                                                                |
| `tool_load_errors`  | count | Number of errors loading the LLM tool.                                                               |
| `tool_load_state`   | gauge | Status of the LLM tools. 0=Initializing, 1=Ready, 2=Disabled, 3=Error, 4=Refreshing, 5=ShuttingDown. |
| `view_load_errors`  | count | Number of errors loading the view.                                                                   |
| `view_load_state`   | gauge | Status of the views. 0=Initializing, 1=Ready, 2=Disabled, 3=Error, 4=Refreshing, 5=ShuttingDown.     |

### Runtime

| Metric                              | Type      | Description                                         |
| ----------------------------------- | --------- | --------------------------------------------------- |
| `component_metric_registered_count` | gauge     | Number of currently registered component metrics.   |
| `runtime_http_server_started`       | count     | Indicates the runtime HTTP server has started.      |
| `runtime_flight_server_started`     | count     | Indicates the runtime Flight server has started.    |
| `secrets_store_load_duration_ms`    | histogram | Duration in milliseconds to load the secret stores. |
| `worker_active_count`               | gauge     | Number of currently loaded workers.                 |
| `workers_load_duration_ms`          | histogram | Duration in milliseconds to load the worker.        |

{% hint style="info" %}
In addition to these core metrics, individual components can expose their own metrics. For example, the MySQL data connector exposes connection pool metrics. See the [Spice OSS Observability docs](https://spiceai.org/docs/features/observability) for more details.
{% endhint %}

See also:

* [Monitoring](../monitoring/) - Set up dashboards with Grafana, Prometheus, or Datadog
* [Projects API](management/) - Manage your projects
* [API Keys](management/) - Manage project API keys
* [Observability](../../features/observability/) - Observability features in the portal
