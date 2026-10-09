---
icon: cubes
---

# Projects

**Projects** are self-contained instances of Spice OSS Runtime, running in Spice.ai Cloud Platform.

Each project has a unique API Key and owned by individual accounts or [**organizations**](../organizations.md).

{% hint style="info" %}
Projects were previously called apps. The portal now uses **Project** throughout, and the Management API serves canonical `/v1/projects` routes alongside the supported legacy `/v1/apps` paths. See [Management APIs](../../api/management/).
{% endhint %}

### Project names

A project name must be 4–38 characters long and may contain only letters, numbers, and hyphens. Names are unique within an organization and are matched case-insensitively, so `analytics` and `Analytics` cannot both exist in the same organization.

The name forms part of the project's URL in the portal, and renaming a project after it is created is not supported. To use a different name, create a new project under that name.

### Learn how to:

* [Transfer a project to another organization](transfer.md)
* [Connect a project with your existing GitHub repository](connect-github.md)
* [Pause and resume a project](pause.md)
* [Delete a project](delete.md)
* [Create and toggle custom Dataset and Views](../datasets-and-views.md)
