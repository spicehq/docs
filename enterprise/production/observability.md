---
description: Metrics, logs, traces, and alerts for production Spice.ai Enterprise deployments.
icon: chart-line
---

# Observability

Spice.ai Enterprise exposes a comprehensive set of metrics, structured logs, and OpenTelemetry traces. This page documents how to wire each into a production observability stack and which signals to alert on.

## Metrics

The runtime exposes Prometheus metrics on port `9090` at `/metrics`. The Helm chart and the Kubernetes operator both ship with first-class scrape integration:

- **Helm chart**: set `monitoring.podMonitor.enabled: true` to deploy a `PodMonitor`.
- **Operator**: set `servicemonitor.enabled: true` to deploy a `ServiceMonitor` for the operator itself.

```yaml
# values.yaml (Spice Helm chart)
monitoring:
  podMonitor:
    enabled: true
    additionalLabels:
      release: prometheus
```

### Key metrics

Metric names are exported verbatim, without a namespace prefix and without unit or counter suffixes. Durations are milliseconds, and the unit is part of the name.

| Metric                                        | Type      | Labels     | Meaning                                                                        |
| --------------------------------------------- | --------- | ---------- | ------------------------------------------------------------------------------ |
| `query_duration_ms`                           | Histogram |            | End-to-end query latency, in milliseconds.                                     |
| `query_execution_duration_ms`                 | Histogram |            | Query execution latency, excluding planning.                                   |
| `query_executions`                            | Counter   |            | Queries executed. The denominator for the [error rate alert](#alerts).          |
| `query_failures`                              | Counter   | `err_code` | Failed queries, by error code.                                                 |
| `dataset_acceleration_refresh_duration_ms`    | Histogram | `dataset`  | Per-dataset acceleration refresh latency.                                      |
| `dataset_acceleration_refresh_errors`         | Counter   | `dataset`  | Refresh failures per dataset.                                                  |
| `dataset_acceleration_last_refresh_unix_time_ms` | Gauge  | `dataset`  | When the last refresh completed, as a Unix timestamp in milliseconds.           |
| `dataset_acceleration_size_bytes`             | Gauge     | `dataset`  | Size of the accelerated table storage.                                         |
| `http_requests_duration_ms`                   | Histogram |            | HTTP API latency.                                                              |
| `flight_request_duration_ms`                  | Histogram |            | Arrow Flight RPC latency.                                                      |
| `process_resident_memory_bytes`               | Gauge     |            | Resident set size of the `spiced` process.                                     |
| `query_memory_pool_used_bytes`                | Gauge     |            | Memory currently held by the query memory pool.                                |
| `cayenne_compaction_memory_pool_bytes`        | Gauge     |            | Size of the dedicated compaction memory pool, when one is reserved.             |
| `cayenne_compaction_memory_pool_used_bytes`   | Gauge     |            | Memory currently held by the compaction memory pool.                           |
| `cayenne_compaction_memory_exhausted_total`   | Counter   | `table`, `kind` | Compaction passes that could not reserve memory from the compaction pool. |
| `spiced_cpu_budget_cores`                     | Gauge     | `source`   | Cores the runtime sized itself for, and where that value came from. See [CPU sizing](../kubernetes/user-guide.md#request-cpu-and-memory). |
| `scheduler_active_executors_count`            | Gauge     | `node_id`  | (`SpicepodCluster`) Executors registered with the scheduler.                    |
| `scheduler_job_queue_depth`                   | Gauge     | `node_id`  | (`SpicepodCluster`) Queued jobs awaiting scheduling.                            |
| `spiceai_operator_reconcile_duration_seconds` | Histogram | `controller` | Operator reconcile loop latency.                                             |
| `spiceai_operator_cluster_ca_expiry_timestamp_seconds` | Gauge | `cluster` | Cluster CA certificate expiry, as a Unix timestamp in seconds.              |

The `spiced_cpu_budget_cores` `source` label reports how the entitlement was determined: `configured`, `cgroup_quota`, `affinity`, or `fallback`.

For the full list, query the running runtime: `curl localhost:9090/metrics | grep -E '^# HELP'`.

{% hint style="info" %}
Query metrics are reported regardless of the `runtime.task_history` setting, so disabling task history does not disable the query counters and histograms above.
{% endhint %}

### Memory pools

Memory budgets are derived from the running process's own cgroup limit, so a container sees the memory it was allocated rather than the memory of its host.

A dedicated compaction memory pool is reserved only for [Cayenne](https://spiceai.org/docs/components/data-accelerators/cayenne) accelerations that can compact into it — file mode with a small-write refresh profile. Other deployments, including `refresh_mode: full`, keep the whole memory limit available to queries.

Compare `query_memory_pool_used_bytes` against the memory limit to see query headroom. A rising `cayenne_compaction_memory_exhausted_total` means compaction cannot reserve the memory its rewrite working set needs; raise the memory limit for the deployment.

When a memory pool refuses a reservation, the query fails with the `ResourcesExhausted` error code, the HTTP API returns `503 Service Unavailable`, and Arrow Flight reports `RESOURCE_EXHAUSTED`. Both are retriable, so pool exhaustion stays distinguishable from a rejected query, which returns `400`. Alert on it through the `err_code` label:

```
sum(rate(query_failures{err_code="ResourcesExhausted"}[5m])) > 0
```

### Cache metrics

Each cache the runtime maintains exports its own metric family. The SQL results cache uses the `results_` prefix; the search results and embeddings caches use `search_results_` and `embeddings_` and expose the same metric names.

| Metric                                     | Type    | Labels   | Meaning                                                                                                                         |
| ------------------------------------------ | ------- | -------- | ------------------------------------------------------------------------------------------------------------------------------- |
| `results_cache_requests`                   | Counter |          | Lookups against the cache.                                                                                                      |
| `results_cache_hits`                       | Counter |          | Lookups served from the cache.                                                                                                  |
| `results_cache_misses`                     | Counter |          | Lookups not served from the cache.                                                                                              |
| `results_cache_hit_ratio`                  | Gauge   |          | Hits divided by total requests.                                                                                                 |
| `results_cache_items_count`                | Gauge   |          | Entries currently held.                                                                                                         |
| `results_cache_size_bytes`                 | Gauge   |          | Size of the cache in bytes.                                                                                                     |
| `results_cache_max_size_bytes`             | Gauge   |          | Configured `max_size`, in bytes.                                                                                                |
| `results_cache_evictions`                  | Counter | `reason` | Entries removed from the cache, by cause.                                                                                       |
| `results_cache_stale_rejections`           | Counter |          | Lookups that found an entry but did not serve it, because a table it read was invalidated first. Also counted as misses.        |
| `results_cache_stale_swr_count`            | Counter |          | Stale-while-revalidate refreshes skipped because a revalidation was already in flight.                                          |
| `results_cache_swr_background_query_count` | Counter |          | Background queries started for stale-while-revalidate refreshes.                                                                |

The `reason` label on `*_cache_evictions` separates three causes that call for different responses:

| `reason`      | Cause                                                                | Response                                                                    |
| ------------- | -------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| `size`        | The cache exceeded `max_size` and reclaimed an entry.                | Raise `max_size` if the hit ratio is also falling.                          |
| `expired`     | The entry outlived `item_ttl`.                                       | Expected. Raise `item_ttl` only if the data tolerates a longer staleness window. |
| `invalidated` | A refresh or a DML write dropped the entries referencing a table.    | Expected on an accelerated dataset with a periodic refresh.                 |

On an accelerated dataset with a periodic refresh, `invalidated` is normally the dominant cause and would swamp an unlabelled total. Alert on `size` instead, which is the reason that indicates real cache pressure:

```
sum(rate(results_cache_evictions{reason="size"}[5m])) > 0
```

{% hint style="info" %}
Cache counters are published at zero when the runtime starts, so each series exists before it first increments. A series that is absent altogether therefore indicates a scrape or configuration problem rather than an idle cache.
{% endhint %}

### Cayenne segment cache

[Cayenne](https://spiceai.org/docs/components/data-accelerators/cayenne) accelerations read through one in-memory segment cache shared by every Cayenne table in the process. Size it with `cayenne_segment_cache_mb` under `runtime.params`:

```yaml
runtime:
  params:
    cayenne_segment_cache_mb: 1024
```

When unset, the budget is approximately 1/64 of the detected memory entitlement, clamped to between `256` MiB and `2` GiB. The parameter counts binary megabytes, so `cayenne_segment_cache_mb: 1024` reserves 1 GiB. A value of `0` disables segment caching. Every series carries a `cache` label; the shared cache reports as `shared`.

{% hint style="info" %}
`cayenne_segment_cache_mb` is only read from `runtime.params`. A value set on an individual `acceleration` or `catalog` is ignored, and the runtime reports it at startup against the dataset or catalog that set it.
{% endhint %}

| Metric                                 | Type    | Labels  | Meaning                                       |
| -------------------------------------- | ------- | ------- | --------------------------------------------- |
| `cayenne_segment_cache_accesses`       | Counter | `cache` | Segment cache lookups.                        |
| `cayenne_segment_cache_hits`           | Counter | `cache` | Lookups served from the cache.                |
| `cayenne_segment_cache_entries`        | Gauge   | `cache` | Approximate number of entries held.           |
| `cayenne_segment_cache_weighted_bytes` | Gauge   | `cache` | Approximate size of the live cache, in bytes. |
| `cayenne_segment_cache_capacity_bytes` | Gauge   | `cache` | Configured capacity, in bytes.                |

The accesses and hits series are cumulative counters, so read them with `rate()` or `increase()` rather than as instantaneous values. A hit ratio that falls while `cayenne_segment_cache_weighted_bytes` sits at `cayenne_segment_cache_capacity_bytes` means the combined working set of every Cayenne table no longer fits; raise `runtime.params.cayenne_segment_cache_mb`.

## Grafana dashboard

Spice.ai publishes a maintained Grafana dashboard with the panels operations teams need most often (query rate / latency / errors, acceleration freshness and row counts, executor registration, certificate expiry).

Import via dashboard ID or copy the JSON from the [Spice.ai Grafana dashboard](https://spiceai.org/docs/monitoring/grafana). The dashboard is compatible with Prometheus, Amazon Managed Prometheus, Azure Managed Prometheus, and Google Cloud Managed Service for Prometheus.

## Logs

Spice emits structured JSON logs on stdout. The log level is controlled by `SPICED_LOG`:

| Level   | Use                                                                |
| ------- | ------------------------------------------------------------------ |
| `ERROR` | Production default.                                                |
| `WARN`  | Production with elevated visibility.                               |
| `INFO`  | Default during cutover and incident investigation.                 |
| `DEBUG` | Development and targeted debugging only \u2014 not for production. |

```yaml
spec:
  env:
    - name: SPICED_LOG
      value: INFO
```

### Log routing

| Destination                  | Recommended forwarder                                                       |
| ---------------------------- | --------------------------------------------------------------------------- |
| **CloudWatch Logs**          | Fluent Bit DaemonSet with the `cloudwatch_logs` output plugin.              |
| **Azure Monitor**            | [Container insights](https://learn.microsoft.com/azure/azure-monitor/containers/container-insights-overview). |
| **Google Cloud Logging**     | The GKE-managed logging agent (default on Autopilot / Standard).            |
| **Datadog**                  | Datadog Agent with the Kubernetes integration enabled.                      |
| **Elastic / OpenSearch**     | Filebeat or Fluent Bit with the `elasticsearch` / `opensearch` output.       |
| **Loki**                     | [Grafana Alloy](https://grafana.com/docs/alloy/) or Promtail.                |

Always retain query-error and acceleration-refresh-failure log lines for at least 30 days for incident review.

## Distributed tracing

When the runtime is started with `--otel-endpoint` or the `SPICED_OTEL_ENDPOINT` environment variable, Spice exports OpenTelemetry traces over OTLP/gRPC. Traces cover query parsing, optimization, and execution; for `SpicepodCluster`, scheduler-to-executor RPCs are linked into a single trace.

```yaml
spec:
  env:
    - name: SPICED_OTEL_ENDPOINT
      value: http://otel-collector.observability:4317
    - name: SPICED_OTEL_RESOURCE_ATTRIBUTES
      value: service.name=spiceai,deployment.environment=prod
```

Pair with an OpenTelemetry Collector configured for the organization's tracing backend (Tempo, Jaeger, Datadog, Honeycomb, X-Ray).

## Alerts

The following alerts are the minimum recommended set for production. Tune thresholds against the deployment's observed baseline.

```yaml
groups:
  - name: spiceai
    interval: 30s
    rules:
      - alert: SpiceAIQueryErrorRateHigh
        expr: |
          sum(rate(query_failures[5m]))
            /
          sum(rate(query_executions[5m])) > 0.05
        for: 10m
        labels:
          severity: warning
        annotations:
          summary: "Query error rate above 5% for 10 minutes."

      - alert: SpiceAIAccelerationRefreshFailing
        expr: |
          increase(dataset_acceleration_refresh_errors[15m]) > 3
        for: 15m
        labels:
          severity: warning
        annotations:
          summary: "Acceleration refresh failing for {{ $labels.dataset }}."

      - alert: SpiceAIQueryLatencyP95
        expr: |
          histogram_quantile(0.95, sum by (le) (
            rate(query_duration_ms_bucket[5m])
          )) > 2000
        for: 10m
        labels:
          severity: warning
        annotations:
          summary: "p95 query latency above 2s."

      - alert: SpiceAIExecutorMissing
        expr: |
          scheduler_active_executors_count < 2
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "Scheduler {{ $labels.node_id }} has fewer than 2 executors registered."

      - alert: SpiceAIClusterCAExpiringSoon
        expr: |
          spiceai_operator_cluster_ca_expiry_timestamp_seconds - time() < 7 * 24 * 3600
        for: 10m
        labels:
          severity: warning
        annotations:
          summary: "{{ $labels.cluster }} CA certificate expires in less than 7 days."
```

The latency threshold is in milliseconds, matching the `query_duration_ms` histogram.

{% hint style="info" %}
`spiceai_operator_cluster_ca_expiry_timestamp_seconds` is emitted by the Kubernetes Operator, not the runtime, so this alert requires the operator to be scraped as well. See [Operator Metrics](../kubernetes/metrics.md).
{% endhint %}

Crashloop protection is not exposed as a metric. The operator records it on the resource as a pause reason and emits Kubernetes events \u2014 see [Crashloop Protection](../kubernetes/spicepodset.md#crashloop-protection). Alert on container restarts using the `kube_pod_container_status_restarts_total` series from kube-state-metrics.

## Health endpoints

| Endpoint            | Purpose                                                                                        |
| ------------------- | ---------------------------------------------------------------------------------------------- |
| `GET /health`       | Liveness. Returns `200 OK` when the process is responsive. Use for container liveness probes.   |
| `GET /v1/ready`     | Readiness. Returns `200 OK` only once datasets and accelerations have completed initial load. Use for container readiness probes and load balancer health checks. |
| `GET /metrics`      | Prometheus metrics on port `9090`. Should never be exposed externally.                          |

The Spice Helm chart and the operator configure liveness and readiness probes against these endpoints by default. Tune the probe parameters on heavy initial-load workloads via [`probes`](../kubernetes/spicepodset.md):

```yaml
spec:
  probes:
    readiness:
      initialDelaySeconds: 30
      periodSeconds: 10
      failureThreshold: 6
    liveness:
      initialDelaySeconds: 60
      periodSeconds: 30
      failureThreshold: 5
```

### Gating readiness on executor availability (scheduler role)

In distributed (`SpicepodCluster`) deployments a scheduler can finish loading its own datasets and accelerations before enough executors have connected to actually serve queries. To keep a scheduler out of rotation until the cluster has capacity, `GET /v1/ready` accepts two optional executor gates. They apply only to the **scheduler** role; supplying a non-zero value on a non-scheduler node returns `400`.

| Query parameter                | Description                                                                                                                                                                                                                            |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `min_ready_executors`          | Minimum number of currently-ready executors required for the probe to succeed. "Ready" means the scheduler holds a live FlightSQL client for the executor (i.e. it can route queries to it). `0` disables the gate.                     |
| `min_ready_executors_percent`  | Minimum percentage (`0`–`100`) of ready executors relative to the executors currently registered (control stream open). `0` disables the gate. Values above `100` return `400`.                                                        |
| `verbose`                      | When `true`, the response body becomes a multi-line diagnostic listing the result of each gate. The HTTP status code is unchanged — useful for `kubectl describe` and `curl` debugging.                                                |

When both gates are supplied, **both** must pass for the probe to return `200 OK`; otherwise the endpoint returns `503` (gate not yet satisfied) or `400` (invalid parameter, e.g. a percentage outside `0`–`100`, or a non-zero gate requested outside scheduler role). Datasets and accelerations must still have completed their initial load regardless of executor gating.

Pass the gates as query parameters on the readiness probe path:

```yaml
readinessProbe:
  httpGet:
    path: /v1/ready?min_ready_executors=3&min_ready_executors_percent=80
    port: 8090
```
