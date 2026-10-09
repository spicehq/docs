---
icon: circle-pause
---

# Pause and Resume

Pausing a project stops its running Spice runtime without deleting anything. Settings, configuration, and data are preserved, and the project can be resumed at any time.

Pause a project that is not currently needed — a demo between sessions, or a project waiting on upstream work — rather than deleting and rebuilding it.

{% hint style="info" %}
Pausing is reversible. To remove a project permanently instead, see [Delete](delete.md).
{% endhint %}

## Pause a project

1. Navigate to the project you want to pause.
2. Click **Settings** in the project navigation sidebar.
3. Scroll down to the **Danger Zone** section.
4. Click the **Pause** button on **Pause project**.
5. In the **Pause** dialog, click **Pause** to confirm.

The running instance is stopped once the pause is confirmed.

{% hint style="info" %}
Pausing and resuming a project requires the **member** role or above in the owning organization. Members with the viewer role cannot pause or resume a project.
{% endhint %}

## While a project is paused

The project remains listed in the portal and its settings stay editable, so configuration can be changed before it is resumed.

The **Playground** and **Observability** pages have no runtime to serve them while the project is paused. Both show a card reporting that the project is paused, with a **Resume** button.

A paused project serves no queries. Requests made against its API key while it is paused do not reach a runtime.

## Resume a project

Resume a project from either place:

* On the project **Settings** page, in the **Danger Zone** section, click **Resume** on **Resume project**.
* On the **Playground** or **Observability** page, click **Resume** on the paused-project card.

The runtime is redeployed from the project's existing configuration. The portal reports progress and waits for the runtime to report ready before returning to the page.

## Inactivity auto-pause

Projects on the Community plan are paused automatically after a period of inactivity. Paid plans are not auto-paused. See [Community Plan](../../pricing/community.md) for the inactivity window and what counts as activity.

A project paused for inactivity is resumed the same way as one paused manually.
