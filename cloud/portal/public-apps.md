---
description: Publish a project to https://spicerack.org
icon: globe
---

# Public Projects

Public Spice projects can be forked, added as a dependency or connected to using Spice OSS Spice.ai connector.

### Making the project public

{% hint style="info" %}
The project must be connected to a public GitHub repository to be made public.\
Check out how to connect a project to the repository - [connect GitHub](apps/connect-github.md).
{% endhint %}

To make your project public, go to your project settings, click **Publish project** in the **Project visibility** section, then confirm with **Make public**.

After that, the project will be visible to all users at `https://spice.ai/<org-name>/<project-name>` and searchable at [https://spicerack.org](https://spicerack.org).

### On the registry

A public project is indexed on [SpiceRack](https://spicerack.org), the package registry for Spicepods. Its package page sits at `https://spicerack.org/<org-name>/<project-name>` and lists the datasets, models, and dependencies declared in the project's Spicepod manifest.

Other users install the published Spicepod in one of three ways:

```bash
spice add "<org-name>/<project-name>"
```

```bash
spice connect "<org-name>/<project-name>"
```

```yaml
dependencies:
  - <org-name>/<project-name>
```

See [SpiceRack Registry](spicerack.md) for browsing, search, and the registry API.
