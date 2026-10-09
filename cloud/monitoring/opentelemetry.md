---
description: Push Spice Cloud metrics to any OpenTelemetry-compatible backend over OTLP
icon: satellite-dish
---

# OpenTelemetry

Spice Cloud can **push** metrics to any OpenTelemetry-compatible backend over OTLP, as an alternative to having a monitoring agent **scrape** the [metrics endpoint](../api/metrics.md).

Pushing is configured in the project's [spicepod](../portal/app-spicepod/spicepod-configuration.md) under `runtime.telemetry.otel_exporter`. No collector or agent is required — the runtime exports directly to the endpoint.

```yaml
runtime:
  telemetry:
    otel_exporter:
      endpoint: https://otlp.example-org.com/v1/metrics
      headers:
        api-key: ${secrets:OTLP_API_KEY}
```

### Push or scrape

Both paths expose the same runtime metrics. They differ in which side initiates the connection:

| | Scrape | Push (OTLP) |
| --- | --- | --- |
| Initiated by | The monitoring agent | The Spice runtime |
| Configured in | The agent | The project spicepod |
| Reachability | Agent must reach the project endpoint | Runtime must reach the backend |
| Temporality | Always cumulative | `delta` by default, configurable |

Push suits backends that offer a hosted OTLP intake, and avoids running an agent that has network access to the project. Scrape suits an existing Prometheus-based stack. See [Grafana & Prometheus](grafana.md) and [Datadog](datadog.md) for the scrape path.

### Configuration reference

**`runtime.telemetry`**

| Field | Type | Default | Description |
| ----- | ---- | ------- | ----------- |
| `enabled` | boolean | `true` | Whether runtime telemetry is collected |
| `properties` | map | `{}` | Custom key/value pairs attached to every metric as OpenTelemetry resource attributes |
| `metric_prefix` | string | none | Prefix prepended to every exported metric name |
| `otel_exporter` | object | none | OTLP push configuration (below) |

**`runtime.telemetry.otel_exporter`**

| Field | Type | Default | Description |
| ----- | ---- | ------- | ----------- |
| `endpoint` | string | — | **Required.** For HTTP, the full URL. For gRPC, a hostname with optional port |
| `enabled` | boolean | `true` | Whether the exporter is active |
| `push_interval` | string | `60s` | How often metrics are pushed |
| `metrics` | list | all | Whitelist of metric names to export |
| `headers` | map | none | Headers sent with each export. For gRPC these become metadata entries and keys must be lowercase |
| `temporality` | string | `delta` | How counters and histograms are encoded — see below |

### Temporality

`temporality` controls how counter and histogram values are encoded on the wire:

| Value | Behavior | When to use |
| ----- | -------- | ----------- |
| `delta` | Each export carries the change since the previous export | Default. Required by Datadog's OTLP intake and recommended by New Relic, AWS CloudWatch, and most hosted OTLP backends |
| `cumulative` | Each export carries the running total since process start | Collectors that feed Prometheus or other cumulative-native backends |
| `low_memory` | Counters cumulative, histograms delta | Histogram-heavy workloads, to reduce in-process state |

{% hint style="info" %}
`temporality` affects only the OTLP push exporter. The [metrics endpoint](../api/metrics.md) always exposes cumulative metrics regardless of this setting.
{% endhint %}

### Namespacing metrics

`metric_prefix` prepends a string to every exported metric name, which avoids collisions when several services report into one backend:

```yaml
runtime:
  telemetry:
    metric_prefix: 'spiceai.'
```

The runtime metric `query_duration_ms` is then exported as `spiceai.query_duration_ms`.

{% hint style="warning" %}
The `metrics` whitelist is applied **after** the prefix. When `metric_prefix` is set, entries must include it — `query_duration_ms` will not match once the prefix is `spiceai.`, so use `spiceai.query_duration_ms`.
{% endhint %}

### Custom attributes

`properties` attaches key/value pairs to every metric as OpenTelemetry resource attributes, which backends surface as queryable dimensions:

```yaml
runtime:
  telemetry:
    properties:
      environment: prod
      region: us-west-2
      team: data-platform
```

### Full example

```yaml
runtime:
  telemetry:
    metric_prefix: 'spiceai.'
    properties:
      environment: prod
      region: us-west-2
    otel_exporter:
      endpoint: https://otlp.example-org.com/v1/metrics
      push_interval: '30s'
      temporality: delta
      headers:
        api-key: ${secrets:OTLP_API_KEY}
```

Store credentials as [secrets](../portal/apps/secrets.md) and reference them with `${secrets:NAME}` rather than writing them into the spicepod.

### Related

* [New Relic](new-relic.md) — a worked OTLP example
* [Metrics API](../api/metrics.md) — endpoint reference and the full metric list
* [Grafana & Prometheus](grafana.md) and [Datadog](datadog.md) — the scrape path
