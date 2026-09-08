---
description: Monitor Spice Cloud with New Relic
icon: chart-line
---

# New Relic

Spice Cloud can be monitored with [New Relic](https://newrelic.com/) two ways: pushing metrics directly to New Relic's hosted OTLP intake, or scraping the [metrics endpoint](../api/metrics.md) with the New Relic infrastructure agent.

The OTLP path is covered first because it needs no agent, and the New Relic license key can be held as a project [secret](../portal/apps/secrets.md) rather than configured on a host.

## OpenTelemetry OTLP export

Add an `otel_exporter` block to the project's [spicepod](../portal/app-spicepod/spicepod-configuration.md):

```yaml
runtime:
  telemetry:
    otel_exporter:
      endpoint: https://otlp.nr-data.net/v1/metrics
      headers:
        api-key: ${secrets:NEW_RELIC_LICENSE_KEY}
```

Pick the endpoint matching the account's region:

| Region | OTLP/HTTP endpoint |
| ------ | ------------------ |
| US (default) | `https://otlp.nr-data.net/v1/metrics` |
| EU | `https://otlp.eu01.nr-data.net/v1/metrics` |
| FedRAMP | `https://gov-otlp.nr-data.net/v1/metrics` |

The header name is `api-key`, lowercase. Use a New Relic [license key](https://docs.newrelic.com/docs/apis/intro-apis/new-relic-api-keys/#license-key) — either the account ingest license key or an ingest-specific key. Store it as a project [secret](../portal/apps/secrets.md) and reference it with `${secrets:NAME}`.

Metrics appear in New Relic's Metrics Explorer within a minute or two of the first push.

{% hint style="info" %}
Spice exports with `delta` temporality by default, which is what New Relic expects. See [OpenTelemetry](opentelemetry.md#temporality) if a different setting is needed.
{% endhint %}

### Namespacing and attributes

`metric_prefix` avoids collisions with other services reporting into the same New Relic account, and `properties` become resource attributes that New Relic exposes as queryable dimensions:

```yaml
runtime:
  telemetry:
    metric_prefix: 'spiceai.'
    properties:
      environment: prod
      region: us-west-2
      team: data-platform
    otel_exporter:
      endpoint: https://otlp.nr-data.net/v1/metrics
      push_interval: '30s'
      headers:
        api-key: ${secrets:NEW_RELIC_LICENSE_KEY}
```

Those attributes are then available in NRQL through `WHERE` and `FACET`:

```sql
SELECT average(spiceai.query_duration_ms) FROM Metric WHERE environment = 'prod' FACET region SINCE 1 hour ago
```

{% hint style="warning" %}
When `metric_prefix` is combined with the `metrics` whitelist, the whitelist entries must include the prefix — the filter runs after the prefix is applied.
{% endhint %}

## Prometheus scrape

The New Relic infrastructure agent can scrape the metrics endpoint instead, which suits an estate already collecting Prometheus targets through New Relic.

```bash
curl "https://<project-cname>.spiceai.io/v1/metrics" \
  -H "X-API-Key: <API_KEY>"
```

Point a Prometheus scrape target at that URL, passing the [project API key](../portal/apps/api-keys.md) as the `X-API-Key` header. See New Relic's [Prometheus integrations overview](https://docs.newrelic.com/docs/infrastructure/prometheus-integrations/get-started/send-prometheus-metric-data-new-relic/) for agent configuration.

Metrics collected this way are cumulative, since the scrape endpoint always exposes cumulative values.

## Related

* [OpenTelemetry](opentelemetry.md) — full `runtime.telemetry` reference
* [Metrics API](../api/metrics.md) — endpoint reference and the full metric list
* [Grafana & Prometheus](grafana.md) and [Datadog](datadog.md) — other monitoring integrations
