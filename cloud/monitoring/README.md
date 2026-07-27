---
description: Monitor Spice Cloud apps with Prometheus, Grafana, Datadog, and OpenTelemetry
icon: chart-mixed
---

# Monitoring

Spice Cloud exposes a [Prometheus-compatible metrics endpoint](../api/metrics.md) (`/v1/metrics`) on each app instance. Use it to scrape runtime metrics and build dashboards and alerts in your monitoring platform of choice.

```bash
curl https://<app-cname>.spiceai.io/v1/metrics \
  -H "X-API-Key: <API_KEY>"
```

Pre-built dashboards and setup guides are available:

* [Portal](portal.md) - Built-in monitoring dashboard in the Spice.ai Cloud portal
* [Grafana & Prometheus](grafana.md) - Scrape metrics with Prometheus, visualize in Grafana
* [Datadog](datadog.md) - Scrape metrics with the Datadog Agent's OpenMetrics integration
* [Zipkin](zipkin.md) - Distributed tracing with Zipkin

For the full list of available metrics, see the [Metrics API reference](../api/metrics.md#available-metrics).

{% hint style="warning" %}
The metrics endpoint provides Spice runtime metrics only. Kubernetes pod-level metrics (CPU, memory, etc.) are not currently included.
{% endhint %}
