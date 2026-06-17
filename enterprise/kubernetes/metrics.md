---
description: Prometheus scraping and OTLP push for Spice.ai Kubernetes Operator self-telemetry.
icon: gauge-high
---

# Operator Metrics

The operator emits self-telemetry about its own behavior — controller reconcile counts and durations, Kubernetes API call latencies, managed-resource counts, and certificate expiry — through two independent readers:

1. **Prometheus scraping** — always on. The operator exposes a `/metrics` endpoint for pull-based collection.
2. **OTLP push** — opt-in. The operator pushes metrics to an OpenTelemetry collector or a compatible vendor endpoint.

Both readers can run at the same time and observe the same instruments.

{% hint style="info" %}
These are the **operator's** metrics. Metrics emitted by the Spiced runtime inside each Spicepod are exposed on each pod's own metrics port (default `9090`) and the managed `Service`; see the [SpicepodSet reference](spicepodset.md#ports).
{% endhint %}

## Prometheus scraping

The operator serves Prometheus metrics on its metrics bind address (`--metrics-bind-address`, default `0.0.0.0:9090`) at `/metrics`. This reader is always enabled.

```bash
kubectl -n spiceai-operator-system port-forward deploy/spiceai-operator 9090:9090
curl http://localhost:9090/metrics
```

If the Prometheus Operator is installed, enable a `ServiceMonitor` so the operator is scraped automatically:

```yaml
# Helm values for the operator
serviceMonitor:
  enabled: true
  interval: 30s
```

## OTLP push

OTLP export is disabled by default. Enable it through the chart's `telemetry.otlp` values:

```yaml
# Helm values for the operator
telemetry:
  otlp:
    enabled: true
    endpoint: http://otel-collector.observability.svc:4317
    protocol: grpc              # grpc | http/protobuf
    headers:
      authorization: "Bearer ${OTLP_TOKEN}"
    metrics:
      temporality: delta        # cumulative | delta | lowmemory
      pushInterval: 30s          # export interval (default 30s)
```

These values configure the operator through the standard OpenTelemetry SDK environment variables, which you can also set directly (for example via `extraEnv`):

| Environment variable                                | Description                                                     | Example                                 |
| --------------------------------------------------- | --------------------------------------------------------------- | --------------------------------------- |
| `OTEL_EXPORTER_OTLP_ENDPOINT`                       | Base OTLP endpoint for all signals.                             | `http://otel-collector:4317`            |
| `OTEL_EXPORTER_OTLP_METRICS_ENDPOINT`               | Metrics-specific endpoint override.                             | `http://otel-collector:4317/v1/metrics` |
| `OTEL_EXPORTER_OTLP_PROTOCOL`                       | Transport protocol: `grpc` or `http/protobuf`.                  | `grpc`                                  |
| `OTEL_EXPORTER_OTLP_HEADERS`                        | Comma-separated `key=value` headers (e.g. authentication).      | `authorization=Bearer abc123`           |
| `OTEL_EXPORTER_OTLP_METRICS_TEMPORALITY_PREFERENCE` | Aggregation temporality: `cumulative`, `delta`, or `lowmemory`. | `delta`                                 |
| `OTEL_METRIC_EXPORT_INTERVAL`                       | Export interval in milliseconds.                                | `60000`                                 |
| `OTEL_SERVICE_NAME`                                 | `service.name` resource attribute (default `spice-k8s-operator`). | `spice-k8s-operator`                  |
| `OTEL_RESOURCE_ATTRIBUTES`                          | Additional resource attributes as `key=value` pairs.            | `deployment.environment=prod`           |

### Choose the right temporality

The correct aggregation temporality depends on your backend. Most hosted observability vendors expect **delta**, while Prometheus-compatible time-series databases expect **cumulative**.

| Backend                                                    | Recommended `temporality` |
| ---------------------------------------------------------- | ------------------------- |
| Datadog                                                    | `delta`                   |
| AWS CloudWatch                                             | `delta`                   |
| New Relic                                                  | `delta`                   |
| Prometheus, Grafana Mimir, Cortex, Thanos, VictoriaMetrics | `cumulative`              |
| Memory-constrained collectors                              | `lowmemory`               |

{% hint style="warning" %}
Sending the wrong temporality is a common cause of missing or incorrect rates: delta points pushed to a cumulative backend (or vice versa) produce broken counters. When in doubt, match your backend from the table above.
{% endhint %}

`lowmemory` uses delta temporality for counters and histograms (which can be reset after export) while keeping gauges and up-down counters cumulative, minimizing in-process state for resource-constrained deployments.

## Metric prefix

A configurable prefix is prepended to every metric name. It applies to **both** the Prometheus and OTLP readers, so dashboards and alerts stay consistent regardless of how metrics are collected. Set it through the chart (or the corresponding environment variable) to namespace operator metrics within a shared backend, for example `spice_operator_`.

## Metric whitelist (OTLP only)

The OTLP reader supports an optional allow-list to restrict which metrics are exported — useful for limiting cardinality or cost on a hosted backend. When set, only the named metrics are pushed over OTLP.

{% hint style="info" %}
The whitelist applies to the **OTLP reader only**. The Prometheus `/metrics` endpoint always exposes the full set of metrics for local scraping.
{% endhint %}

## Example: OpenTelemetry Collector

A minimal collector configuration that receives the operator's OTLP metrics and forwards them to a backend:

```yaml
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317

processors:
  batch: {}

exporters:
  prometheusremotewrite:
    endpoint: https://mimir.example.com/api/v1/push
  # or: datadog, awsemf, otlphttp, ...

service:
  pipelines:
    metrics:
      receivers: [otlp]
      processors: [batch]
      exporters: [prometheusremotewrite]
```

Point the operator at the collector's OTLP gRPC port (`4317`) and choose `temporality: cumulative` for the Prometheus-remote-write path above, or `delta` when exporting to Datadog / CloudWatch / New Relic.

## Resilient initialization

Telemetry setup is defensive: if the OTLP endpoint is unreachable or misconfigured at startup, the operator logs the error and continues running with the Prometheus reader rather than crashing. Export failures are retried and never block reconciliation, so a metrics outage cannot take down your workloads.

## See also

- [Overview — Helm Values](README.md#helm-values) — the `telemetry.otlp.*` and `serviceMonitor.*` values.
- [SpicepodSet — Monitoring](spicepodset.md#monitoring) — telemetry properties for the Spiced runtime.
