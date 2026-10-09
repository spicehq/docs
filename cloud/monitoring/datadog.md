---
description: Monitor Spice Cloud with Datadog
icon: dog
---

# Datadog

Spice Cloud can be monitored with Datadog using the [Metrics endpoint](../api/metrics.md) and a pre-built dashboard available in the [Spice repository](https://github.com/spiceai/spiceai/blob/trunk/monitoring/datadog-dashboard.json).

## Datadog Agent Configuration

**Prerequisite:** Datadog Agent version 6.5.0 or later is [installed](https://docs.datadoghq.com/agent/).

Configure the Datadog Agent to scrape your Spice Cloud metrics endpoint. Edit the `openmetrics.d/conf.yaml` file in the `conf.d/` folder at the root of your Agent's configuration directory:

```yaml
init_config:

instances:
  - prometheus_url: https://<project-cname>.spiceai.io/v1/metrics
    headers:
      X-API-Key: <API_KEY>
    namespace: spice
    metrics:
      - '*'
```

Replace `<project-cname>` with your project's CNAME (e.g. `us-west-2-prod-aws-data`) and `<API_KEY>` with your [project API key](../portal/apps/api-keys.md).

Restart the Agent to start collecting Spice metrics. Refer to [Prometheus and OpenMetrics metrics collection from a host](https://docs.datadoghq.com/integrations/guide/prometheus-host-collection/) for all available configuration options and supported parameters.

Open the Datadog **Metrics Explorer** and type `spice` to confirm Spice telemetry is successfully collected.

## Import Datadog Dashboard

1. In Datadog, create a **New Dashboard**
2. Click **Import dashboard JSON**
3. Upload the [`datadog-dashboard.json`](https://raw.githubusercontent.com/spiceai/spiceai/trunk/monitoring/datadog-dashboard.json) file

The dashboard provides panels for system health, query performance, data acceleration, caching, AI/ML operations, and Flight protocol metrics.

See also:

* [Metrics API](../api/metrics.md) - Endpoint reference and full list of available metrics
* [Grafana & Prometheus](grafana.md) - Alternative monitoring with Grafana
