---
description: Monitor Spice Cloud with Grafana and Prometheus
icon: chart-line
---

# Grafana & Prometheus

Spice Cloud can be monitored with Grafana using the [Metrics endpoint](../api/metrics.md) and a pre-built dashboard available in the [Spice repository](https://github.com/spiceai/spiceai/blob/trunk/monitoring/grafana-dashboard.json).

## Prometheus Configuration

Configure a Prometheus instance to scrape metrics from your Spice Cloud app:

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: spiceai
    scheme: https
    metrics_path: /v1/metrics
    static_configs:
      - targets: ['<app-cname>.spiceai.io']
    headers:
      X-API-Key: <API_KEY>
```

Replace `<app-cname>` with your app's CNAME (e.g. `us-west-2-prod-aws-data`) and `<API_KEY>` with your [app API key](../portal/apps/api-keys.md).

## Import Grafana Dashboard

1. Navigate to **Dashboards** in Grafana and click **New** > **Import**
2. Copy the dashboard JSON from [`monitoring/grafana-dashboard.json`](https://raw.githubusercontent.com/spiceai/spiceai/trunk/monitoring/grafana-dashboard.json) into the import box
3. Click **Load**

The dashboard provides panels for system health, query performance, data acceleration, caching, AI/ML operations, and Flight protocol metrics.

## Grafana as a Query Tool

In addition to metrics monitoring, Grafana can query Spice Cloud data directly using the FlightSQL or Infinity plugins. See the [Grafana Integration](../integrations/grafana.md) guide for setup instructions.

See also:

* [Metrics API](../api/metrics.md) - Endpoint reference and full list of available metrics
* [Datadog](datadog.md) - Alternative monitoring with Datadog
