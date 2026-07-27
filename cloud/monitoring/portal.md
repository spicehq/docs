---
description: Monitor Spice Cloud apps from the Spice.ai portal
icon: browser
---

# Portal

The Spice.ai Cloud portal includes a built-in monitoring dashboard — no external tools or configuration required.

## Monitoring Dashboard

The **Observability** tab in your app provides real-time visibility into usage, request performance, and API activity:

* **SQL Queries** — total count, success/failure rate, and average duration
* **AI Completions** — LLM inference request metrics
* **Vector Searches** — embedding-based search request metrics
* **Embedding Calculations** — embedding generation metrics
* **Dataset Refreshes** — accelerated dataset refresh success and timing

### Accessing the Dashboard

1. Navigate to your Spice app in the [portal](https://spice.ai)
2. Click the **Observability** tab in the app navigation bar
3. Select a time range: **1 hour**, **24 hours**, **7 days**, or **28 days**

Use the metrics dashboard to identify usage trends, detect spikes in query duration, and plan capacity.

## API Request Logs

The request logs provide a detailed record of individual API requests, including status codes, durations, and timestamps.

1. Open the **Observability** tab for your app
2. Select **Request Logs** in the left navigation
3. Select a time range: **past hour**, **8 hours**, **24 hours**, or **up to 3 days**

Use request logs to debug failing queries, identify slow requests, and audit API usage.

For full details, see the [Portal Monitoring](../portal/monitoring-and-request-logs.md) documentation.

See also:

* [Metrics API](../api/metrics.md) - Scrape metrics programmatically for external dashboards
* [Grafana & Prometheus](grafana.md) - Self-hosted monitoring with Grafana
* [Datadog](datadog.md) - Monitoring with Datadog
