---
description: Use and monitor Spice-managed dedicated clusters in the Portal
icon: server
---

# Dedicated Clusters

A **dedicated cluster** provides Spice-managed, single-tenant infrastructure for your organization. Your Projects run on isolated infrastructure.

{% hint style="info" %}
Dedicated clusters are available to Enterprise plan customers. To request a dedicated cluster, [Talk to Sales](https://meetings.hubspot.com/lukekim/talk-to-sales?uuid=836fd7be-a95e-4cee-b0cb-044fd8ea52a4).
{% endhint %}

## View a dedicated cluster

1. Open your organization in the Portal.
2. Select **Clusters**.
3. Select a dedicated cluster.

The monitoring rail includes:

- **Overview** shows total, requested, and used CPU, memory, and storage. Filter by node and time range.
- **Usage** shows requested and actual resource usage by Project. Filter by Project and time range.
- **Monitors** shows cluster resource monitors and their history.

## Monitor a dedicated cluster

Dedicated cluster monitors alert you when CPU or memory usage on an individual node reaches a threshold.

### Create a monitor

1. Select **Clusters** in your organization.
2. Select a dedicated cluster.
3. Select **Monitors**.
4. Create a **Node CPU usage** or **Node memory usage** monitor template.
5. Set the threshold and notification target.

### Available monitor templates

- **Node CPU usage** fires when node CPU usage exceeds the configured percentage of node capacity.
- **Node memory usage** fires when node memory usage exceeds the configured percentage of node capacity.

CPU and memory use separate monitors. Each monitor measures a percentage against the CPU or memory capacity of each node, not total cluster utilization. If a node crosses the threshold, the monitor fires an alert and sends a notification for that node.

### Configure a monitor

Each monitor has one percentage threshold. By default, the monitor uses critical severity and fires when usage exceeds 85%. Usage must remain above the threshold for 5 minutes before the monitor fires.

Choose a notification destination:

- **Email** sends notifications to selected organization members or email addresses.
- **HTTP** sends a JSON notification with an HTTP `POST` request to an HTTPS URL.

Notifications and monitor history identify the cluster, node, threshold, duration, state, severity, observed percentage when available, and a Portal link.

If a node stops reporting telemetry, the monitor records a warning without an observed percentage.

## Related documentation

- [Dedicated Clusters Management API reference](../../api/management/dedicated-clusters.md)
- [Project monitoring in the Portal](../../monitoring/portal.md)
