---
description: Connect Slack to an organization and use it for monitor alerts
icon: slack
hidden: true
---

# Slack integration

Connect a Slack workspace to a Spice.ai organization to use Slack as a notification destination for project and cluster monitors. An organization administrator connects the workspace and can choose a default alert channel. Each monitor must explicitly use Slack and save its own channel destination; alerts are not sent to the organization default automatically.

## Connect a Slack workspace

1. Open the organization in the Portal and go to **Settings** → **Integrations**.
2. Select **Connect Slack**.
3. In Slack, choose the workspace to connect and approve the requested access.
4. After returning to the Portal, choose a **Default alert channel**.

The default channel is preselected when configuring Slack for a new monitor. You can select a different channel for an individual monitor. Changing the organization default does not change the channel configured for existing monitors.

To use a private channel, invite the Spice Cloud app to that channel so it can be selected. You can change the default channel or disconnect Slack from **Settings** → **Integrations**. While disconnected, monitors configured to send to Slack cannot deliver alerts.

## Use Slack for monitor alerts

Slack is available as a notification destination when creating project and cluster monitors, if Slack is connected for the organization.

1. Open the project's or cluster's **Monitoring** page and start creating a monitor.
2. In the notification settings, select **Slack**.
3. Choose a channel and save the monitor.

The selected channel is saved with that monitor and receives its alerts. The organization default only supplies the initial channel selection; each monitor can target a different channel.

If Slack is not available in the destination list, ask an organization administrator to connect the workspace in **Settings** → **Integrations**.
