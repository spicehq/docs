---
description: Monitor Spice Cloud projects with Prometheus, Grafana, Datadog, and OpenTelemetry
icon: chart-mixed
---

# Monitoring

Spice Cloud exposes a [Prometheus-compatible metrics endpoint](../api/metrics.md) (`/v1/metrics`) on each project instance. Use it to scrape runtime metrics and build dashboards and alerts in your monitoring platform of choice.

```bash
curl "https://<project-cname>.spiceai.io/v1/metrics" \
  -H "X-API-Key: <API_KEY>"
```

Metrics can also be **pushed** to any OpenTelemetry-compatible backend over OTLP, configured in the project spicepod, which needs no scraping agent. See [OpenTelemetry](opentelemetry.md).

Pre-built dashboards and setup guides are available:

* [Portal](portal.md) - Built-in monitoring dashboard in the Spice.ai Cloud portal
* [Grafana & Prometheus](grafana.md) - Scrape metrics with Prometheus, visualize in Grafana
* [Datadog](datadog.md) - Scrape metrics with the Datadog Agent's OpenMetrics integration
* [New Relic](new-relic.md) - Push metrics over OTLP, or scrape with the infrastructure agent
* [OpenTelemetry](opentelemetry.md) - Push metrics to any OTLP-compatible backend
* [Zipkin](zipkin.md) - Distributed tracing with Zipkin

For the full list of available metrics, see the [Metrics API reference](../api/metrics.md#available-metrics).

{% hint style="warning" %}
The metrics endpoint provides Spice runtime metrics only. Kubernetes pod-level metrics (CPU, memory, etc.) are not currently included.
{% endhint %}
