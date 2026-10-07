---
description: >-
  Continuously identify and fix issues by tracking process actions with Spice
  monitoring and request logs.
icon: monitor-waveform
---

# Observability

The **Observability** tab provides real-time visibility into your project's usage, request performance, and API activity. Use it to track request volume, identify failures, and debug issues.

The tab groups the project's metrics, request logs, and traces into sections, listed in the left navigation:

* **Overview** — aggregate metrics for the project.
* **API** — API request metrics.
* **Cache** — results cache metrics. Shown when SQL results caching is enabled.
* **Data** — dataset and acceleration metrics.
* **Resources** — CPU and memory usage for the project's running instances.
* **Request Logs** — a record of individual API requests.
* **Traces** — task traces. Shown for projects with a spicepod.

## Accessing Observability

1. Navigate to your Spice project in the [portal](https://spice.ai).
2. Click the **Observability** tab in the project navigation bar.

## Overview

The **Overview** section shows aggregate metrics for:

* **SQL Queries** — total count, success/failure rate, and average duration.
* **AI Completions** — LLM inference request metrics.
* **Vector Searches** — embedding-based search request metrics.
* **Embedding Calculations** — embedding generation metrics.
* **Dataset Refreshes** — accelerated dataset refresh success and timing.

## Resources

The **Resources** section charts CPU and memory usage per running instance of the project.

1. Open the **Observability** tab for your project.
2. Select **Resources** in the left navigation.

CPU is reported as utilization against the project's configured CPU limit. Projects with no CPU limit set — including projects on dedicated clusters — report absolute core usage instead. Memory is reported in bytes.

Use the resource charts to size a project before raising its limits, and to correlate slow queries with memory or CPU pressure.

## Usage Metrics Dashboard

Track request volume, data usage, and query time across configurable time ranges.

1. Open the **Observability** tab for your project.
2. Select a time range: **1 hour**, **24 hours**, **7 days**, or **28 days**.
3. Review the dashboard charts for request counts, data transferred, and query latency.

Use the metrics dashboard to identify usage trends, detect spikes in query duration, and plan capacity.

## API Request Logs

The request logs provide a detailed record of individual API requests to your project's endpoints, including status codes, durations, and timestamps.

1. Open the **Observability** tab for your project.
2. Select **Request Logs** in the left navigation.
3. Select a time range: **past hour**, **8 hours**, **24 hours**, or **up to 3 days**.
4. Browse the log entries to inspect individual request details including endpoint, status code, and duration.

Use request logs to debug failing queries, identify slow requests, and audit API usage.

## Project monitors

Project monitors notify you when a project signal meets a condition. Unlike charts, they watch for a condition and send a notification. Request Logs show individual API requests for investigation.

1. Open your project and select **Monitoring**.
2. Select **Create monitor**, then choose a monitor type shown for the project.
3. Set its condition in **When**. Available controls depend on the monitor type.
4. Choose one or more destinations in **Then**.
5. Name the monitor and select **Create monitor**.

### Monitor types

The picker shows templates supported by the project. Most let you choose a comparison and threshold. Dataset status lets you choose a dataset and status; instance health has a fixed condition. The evaluation window, sustain period, and severity are set by the template, not edited in the form. A window is the period measured; sustain is how long the condition must hold.

| Monitor | What it watches | Condition and configuration |
| --- | --- | --- |
| **Instance health** | A project instance reporting a failed state, runtime error, or eviction. | Fixed condition; no threshold to configure. Evictions use a fixed 5-minute observation window; critical severity, with no additional sustain delay. |
| **Instance memory** | Memory use as a percentage of the instance's configured memory limit. | Set the comparison and percentage threshold. Default: above 80% over a 5-minute window, sustained for 5 minutes; warning. |
| **Instance CPU** | CPU use as a percentage of the instance's configured CPU limit. | Set the comparison and percentage threshold. Default: above 85% over a 5-minute window, sustained for 5 minutes; critical. Requires a CPU limit. |
| **Dataset status** | A selected dataset reporting a status such as Error. | Choose the dataset and a status: Initializing, Ready, Disabled, Error, Refreshing, or Shutting down. Default: Error over a 5-minute window, sustained for 5 minutes; critical. |
| **Acceleration refresh errors** | Errors recorded during dataset acceleration refreshes. | For projects with acceleration. Set the comparison and error-count threshold. Default: more than 0 errors in a 5-minute window; critical, with no additional sustain delay. |
| **HTTP 5xx responses** | Estimated server-error responses from the project's HTTP API. | Set the comparison and response-count threshold. Default: more than 0 estimated responses in a 5-minute window; critical, with no additional sustain delay. Existing rate-based conditions keep their saved units and values. |
| **SQL p99 latency** | The 99th-percentile duration of SQL queries. | For projects with SQL queries. Set the comparison and threshold in milliseconds. Default: above 1000 ms over a 5-minute window, sustained for 5 minutes; warning. |
| **SQL query failures** | Server-caused SQL query failures. | Requires SQL query telemetry. Set the comparison and failure-count threshold. Default: more than 0 estimated failures in a 5-minute window; critical, with no additional sustain delay. |
| **Flight SQL DoGet failures** | Failed Flight SQL requests. | Requires Flight SQL telemetry. Set the comparison and failure-count threshold. Default: more than 0 estimated failures in a 5-minute window; critical, with no additional sustain delay. |
| **LLM failures** | Server-caused model request failures. | Requires a configured model. Set the comparison and failure-count threshold. Default: more than 0 estimated failures in a 5-minute window; critical, with no additional sustain delay. |

In **Then**, choose one or more destinations: **Email**, **HTTP**, or **Slack**. For Email, select project members or enter email addresses. For HTTP, provide an HTTPS URL and optional authorization token. For Slack, an organization administrator must connect the workspace under **Settings** → **Integrations**. Choose a channel for each monitor; the organization default only preselects a channel for new monitors. See [Connect Slack](../integrations/slack.md).

After saving, select **Send test notification** to check delivery to the configured destinations. The test does not evaluate the monitor condition or add an event to its history.
