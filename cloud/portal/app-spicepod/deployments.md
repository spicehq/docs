---
description: Monitor and manage project Spicepod instances and deployments.
icon: rocket-launch
---

# Deployments

### Create New Deployment

Navigate to the **Deployments** tab and click on **Create Deployment**.

### Deployment Status

Each deployment listed on the **Deployments** tab reports a status derived from the live state of its instances:

| Status                | Meaning                                                                                         |
| --------------------- | ----------------------------------------------------------------------------------------------- |
| **Pending**           | The deployment is queued or created; no instances have started yet.                              |
| **Deploying**         | Instances are starting.                                                                         |
| **Loading**           | Instances are running and loading their initial datasets.                                       |
| **Ready**             | All replicas are ready and serving traffic.                                                     |
| **Ready with errors** | All replicas are ready and passing health checks, but the runtime is reporting errors.           |
| **Unhealthy**         | All required replicas are ready, but at least one is failing its health check.                  |
| **Terminating**       | The deployment is being replaced by a newer deployment that is ready to take traffic.            |
| **Succeeded**         | The deployment completed and its instances are no longer reporting live state.                   |
| **Paused**            | The project is paused, so no deployment is serving.                                                  |
| **Created**           | No live state is available for the deployment, typically because it has already been replaced.    |
| **Failed**            | The deployment failed.                                                                          |
| **Failed to start**   | An instance reported an issue that prevented it from starting, and fewer replicas are serving than the project requested. |

**Ready with errors** distinguishes a deployment that started successfully from one that works. The instances pass their health checks, but the runtime inside them is reporting problems — a dataset that cannot connect, or a model that fails to load. A failing health check is the stronger signal, so a deployment that is both unhealthy and reporting errors shows **Unhealthy**.

A deployment that reports **Failed** or **Failed to start** carries the reason it failed. The portal shows it with the deployment, and the [Management API](../../api/README.md) returns it as the deployment's `error_message`. The reasons separate a project asking for more than the cluster can give from one that reached an account limit:

* Not enough CPU, memory, or storage for the instance the project requested. Reduce the project's requests for that resource.
* Requested project resources too high to place at all. Reduce the project's CPU or memory requests.
* Instance limit reached. Scale down an unused project first.

A failure the platform does not classify reports only that the instance could not start. Contact support for those.

The reasons also differ in whether the platform keeps trying. A shortage of CPU, memory, or storage, and a project that has reached the instance limit, are conditions that can clear on their own. The platform keeps trying to place the instance, the deployment's recorded `status` stays `in_progress`, and it carries the reason in `error_message` while it waits. Once every replica is ready, the deployment records `succeeded` and the reason clears.

Requested resources too high to place at all, and a failure the platform does not classify, cannot clear by waiting. The deployment records `failed` on the first check and the platform stops trying. A reason of that kind on any one instance fails the deployment even when another replica is already serving.

The two statuses differ in how far the instance got. **Failed to start** reports an instance that never began serving, so the reason above names what it lacked. **Failed** reports a deployment the platform has recorded as failed.

While a rollout is underway, the **Deployments** tab in the project navigation carries a count of the deployments still in progress — those reporting **Pending**, **Deploying**, **Loading**, or **Terminating**. The count clears once every deployment has settled.

### Issues

When a deployed Spicepod reports errors or warnings, the portal collects them into a single **Issues** feed instead of leaving them in the log tail.

Issues are surfaced in five places:

* A banner on every page of the project, listing the most recent error and the number of other errors. The banner covers errors only — warnings appear in the **Issues** panel.
* An **Issues** tab in the project navigation. The tab shows a check mark while the project reports no errors, and a warning mark with the current error count when it does. Counts above 99 display as `99+`.
* An **Issues** panel on the **Deployments** page, and on each instance page scoped to that instance. Each panel lists up to five issues and links to the full list.
* A dedicated **Issues** page for the project, opened from the tab, listing every issue without a limit.
* An indicator on the affected row in **Datasets** and **Models**, when an issue can be attributed to a component.

The count on the **Issues** tab always reflects the full feed. Dismissing an error from the banner does not lower it.

The feed combines runtime `ERROR` and `WARN` log lines with the reported status of each dataset. Repeats of the same failure collapse into one row with an occurrence count, so a connector retrying every second appears once rather than hundreds of times.

#### When an issue leaves the feed

The feed reports the current state of the runtime rather than the full log history, so failures that no longer apply drop out on their own.

Once an instance reports that all of its components are loaded, failures recorded only before that point are dropped. Reaching a ready state is treated as evidence that the startup retries succeeded and the optional components came up. A startup failure that occurs again afterwards stays in the feed and keeps its original first-seen time and occurrence count. A dataset that is still broken continues to be reported through its dataset status, so a persistent failure does not disappear.

Failures recorded after an instance is ready leave the feed an hour after their last occurrence. That hour is measured against the newest entry in the log rather than the current time. An instance that crashed and stopped logging therefore still shows the errors it ended on, while one that recovered and keeps logging normally drops the older failure as fresher activity accumulates.

Issues are ordered errors first, then by how often they occurred, then by how recently. A panel capped at five rows therefore shows the errors before any warning, and the panel header keeps the full error and warning counts for the project or instance. **View all** *N* **issues** in the panel footer opens the dedicated **Issues** page, which lists the entire set.

Each row shows where the issue came from, how many times it occurred, when it was last seen, the dataset or model it was attributed to, and the instance that reported it. A failure seen on more than one replica reports the count instead of a single name. **Show details** expands the full text, including stack traces. The link on the row opens the originating instance's logs filtered to the issue, or the component's page for an issue reported by a dataset.

#### Filter by instance

When more than one instance has reported issues, the dedicated **Issues** page shows an instance selector. **All instances** is the default. Selecting an instance narrows the list to the failures that instance reported, and a failure seen on several replicas matches when any one of them is selected.

The selection is held in the `instance` query parameter, so a filtered view survives a reload and can be shared as a link. Opening **View all** *N* **issues** from an instance's panel arrives with that instance already selected.

Issues derived from dataset status are reported by the project rather than by a particular replica, so they appear only under **All instances**.

{% hint style="info" %}
Dismissing an error hides that specific error from the banner only. The **Issues** page, the panels, and the tab count continue to report it. A different failure raises the banner again.

Dismissals are remembered per project in the browser used to dismiss them, so they survive a reload but do not apply to other browsers or to other members of the organization.
{% endhint %}

A project with no issues shows no banner, no count, and no panel. The dedicated **Issues** page remains reachable and reports that no issues were detected.

#### Get AI help

Each issue row has a **Get AI help** action that analyzes that issue on demand. The analysis reads the project's recent runtime logs and returns a likely root cause, a short numbered list of fix steps, links to the relevant documentation, and a confidence level.

Results are generated per issue and are not produced until the action is used.

{% hint style="warning" %}
An AI analysis is a suggestion, not a diagnosis. Verify each step against the project's own configuration before applying it.
{% endhint %}

### Spicepod Instance Logs

Navigate to the **Deployments** tab and click on the **Logs** for the selected instance.
