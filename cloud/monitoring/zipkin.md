---
description: Distributed tracing with Zipkin
icon: magnifying-glass-chart
---

# Zipkin

Spice supports distributed tracing by integrating with Zipkin and compatible tracing systems. Traces capture completed tasks — SQL queries, AI chat completions, tool calls, dataset refreshes — recording execution time, inputs, outputs, and errors.

## Configuration

Enable Zipkin tracing by configuring the `runtime.tracing` section in your app's `spicepod.yaml`:

```yaml
runtime:
  tracing:
    zipkin_enabled: true
    zipkin_endpoint: "http://your_zipkin_host:9411/api/v2/spans"
```

| Parameter         | Default | Description                                                                |
| ----------------- | ------- | -------------------------------------------------------------------------- |
| `zipkin_enabled`  | `false` | Enables or disables Zipkin trace export.                                   |
| `zipkin_endpoint` | —       | Required if enabled. The `/api/v2/spans` endpoint on your Zipkin instance. |

Trace data will be available in the Zipkin UI at `http://your_zipkin_host:9411`. See the [Zipkin Quickstart](https://zipkin.io/pages/quickstart) to run a test server.

## Trace Information

A trace in Spice represents a completed task. Each trace is a unique span recording execution time, inputs, outputs, and errors.

| Field                   | Description                                          |
| ----------------------- | ---------------------------------------------------- |
| `trace_id`              | Unique identifier for the trace                      |
| `span_id`               | Unique identifier for the span                       |
| `task`                  | Task type (e.g. `sql_query`, `text_embed`, `health`) |
| `start_time`            | When the task started                                |
| `end_time`              | When the task completed                              |
| `execution_duration_ms` | Execution duration in milliseconds                   |
| `error_message`         | Error details, if the task failed                    |

Example trace data:

```
trace_id                          | task                | execution_duration_ms | error_message
----------------------------------|---------------------|-----------------------|------------------------------------------
687e0970f8c49d19c5a08764ea2d4dc1  | text_embed          | 16132.4               |
1e881188e5fd252b26adb8a8d838efb8  | sql_query           | 6.1                   |
701874d7282dd47791e7519b343a9694  | accelerated_refresh | 0.4                   |
3c75d16b6b4b8da98c551d115e1c049c  | sql_query           | 0.1                   | SQL error: ParserError("Expected:...")
```

For more details, see [Task History](https://docs.spice.ai/features/observability/task-history).

See also:

* [Portal Monitoring](portal.md) - Built-in monitoring in the Spice.ai portal
* [Metrics API](https://docs.spice.ai/api/runtime-apis/metrics) - Prometheus-compatible metrics endpoint
