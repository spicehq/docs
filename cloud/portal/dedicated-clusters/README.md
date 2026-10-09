---
description: Use and monitor Spice-managed dedicated clusters in the Portal
icon: server
---

# Dedicated Clusters

A **dedicated cluster** provides Spice-managed, single-tenant infrastructure for your organization. Your Projects run on isolated infrastructure.

{% hint style="info" %}
Dedicated clusters are a **Spice.ai Enterprise** feature.
{% endhint %}

## View a dedicated cluster

1. Open your organization in the Portal.
2. Select **Clusters**.
3. Select a dedicated cluster.

The monitoring rail includes:

* **Overview** shows total, requested, and used CPU, memory, and storage. Filter by node and time range.
* **Usage** shows requested and actual resource usage by Project. Filter by Project and time range.
* **Monitors** shows cluster resource monitors and their history.

## Monitor a dedicated cluster

Dedicated cluster monitors alert you when the cluster runs short of CPU, memory, or free capacity. Each monitor compares the sum of the resources in use with the sum of the cluster capacity. One busy node does not fire a monitor while the cluster still has room.

### Create a monitor

1. Select **Clusters** in your organization.
2. Select a dedicated cluster.
3. Select **Monitors**.
4. Create a **Cluster CPU**, **Cluster memory**, or **Cluster availability** monitor.
5. Set the threshold and one or more notification destinations.

### Available monitor templates

* **Cluster CPU** fires when the sum of CPU in use exceeds the configured percentage of the sum of cluster CPU capacity.
* **Cluster memory** fires when the sum of memory in use exceeds the configured percentage of the sum of cluster memory capacity.
* **Cluster availability** fires when the cluster has little free capacity for projects. The monitor divides the unused CPU and the unused memory by the cluster capacity, and it uses the smaller of the two percentages.

If the condition stays true for 5 minutes, the monitor fires an alert and sends a notification for the cluster.

A monitor from the earlier **Node CPU usage** or **Node memory usage** template keeps its per-node evaluation until you save it. When you save it, it becomes a **Cluster CPU** or **Cluster memory** monitor.

### Configure a monitor

Each monitor has one percentage threshold and uses critical severity by default. **Cluster CPU** and **Cluster memory** fire above 85%. **Cluster availability** fires below 15%.

Choose one or more notification destinations. A monitor can use each type of destination one time:

* **Email** sends notifications to selected organization members or email addresses.
* **HTTP** sends a JSON notification with an HTTP `POST` request to an HTTPS URL.
* **Slack** posts notifications to a channel in the Slack workspace that you connect to the organization. See [Slack integration](../../integrations/slack.md).

Notifications and monitor history identify the cluster, the threshold, the duration, the state, the severity, the observed percentage when available, and a Portal link.

If the cluster stops reporting telemetry, the monitor records a warning without an observed percentage.

## Related documentation

* [Dedicated Clusters Management API reference](https://app.gitbook.com/s/xEBUMDTvXMmjEXg5Po1u/management-api/dedicated-clusters)
* [Deployments](../app-spicepod/deployments.md) — status, including `in_progress` with `error_code` when a project cannot start for lack of cluster resources
* [Project monitoring in the Portal](../../monitoring/portal.md)
