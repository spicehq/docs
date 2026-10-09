---
description: Discover, install, and publish Spicepods on https://spicerack.org
icon: boxes-stacked
---

# SpiceRack Registry

[SpiceRack](https://spicerack.org) is the public package registry for Spicepods. It indexes the datasets, models, and AI apps that publishers have made public, and every package installs into any Spice runtime.

Browsing and installing require no account. Publishing requires a public Spice project — see [Public Projects](public-apps.md).

## Browse and search

| Page | URL |
| --- | --- |
| Catalogue | [https://spicerack.org/packages](https://spicerack.org/packages) |
| Search | [https://spicerack.org/search](https://spicerack.org/search) |
| Publisher | `https://spicerack.org/<org>` |
| Package | `https://spicerack.org/<org>/<name>` |

A package page lists the install commands and the datasets, models, views, embeddings, tools, workers, evals, and dependencies declared in the Spicepod manifest.

## Install a package

Each package page offers three ways to consume it.

| Method | Command | When to use |
| --- | --- | --- |
| Spice CLI | `spice add <org>/<name>` | Add the Spicepod as a dependency of the current project. |
| Connect | `spice connect <org>/<name>` | Use a Spicepod hosted on Spice.ai Cloud that requires authentication. |
| `spicepod.yaml` | `dependencies:` entry | Declare the dependency directly in the manifest. |

`spice add` fetches the Spicepod into `./spicepods/<name>/` and registers it under `dependencies:` in `spicepod.yaml`. Append `@<version>` to pin a version:

```bash
spice add spiceai/quickstart@v1.0
```

To declare the same dependency by hand:

```yaml
dependencies:
  - spiceai/quickstart
```

## Registry API

SpiceRack serves the same data it renders as JSON. The API is public, read-only, and requires no authentication. CORS is enabled and responses are cached at the edge for five minutes.

| Endpoint | Returns |
| --- | --- |
| `GET /api/v1` | Registry stats and a self-describing list of endpoints. |
| `GET /api/v1/search` | Packages matching a search term. |
| `GET /api/v1/packages` | The catalogue, optionally filtered to one publisher. |
| `GET /api/v1/packages/{org}/{name}` | Full package metadata, install commands, and manifest contents. |
| `GET /api/v1/orgs/{org}` | Every package published by one organization. |
| `GET /api/v1/stats` | Registry-wide counts and the top publishers. |

`/api/v1/search` accepts `q`, `sort`, `page`, and `per_page`. `/api/v1/packages` accepts `org`, `sort`, `page`, and `per_page`. Sort values are `relevance`, `popular`, `newest`, and `name`; search defaults to `relevance` and the catalogue defaults to `popular`.

```bash
curl https://spicerack.org/api/v1/search?q=taxi
curl https://spicerack.org/api/v1/packages/spiceai/quickstart
```

{% hint style="info" %}
Response fields are additive — new fields may appear, but existing fields are not renamed or removed within a version. Breaking changes ship under a new path prefix.
{% endhint %}

The full reference, including per-parameter defaults, is published at [https://spicerack.org/api](https://spicerack.org/api).

## Publish a package

Make a Spice project public and it is indexed on SpiceRack. See [Public Projects](public-apps.md) for the prerequisites and the steps.
