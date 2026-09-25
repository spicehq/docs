---
description: Manage Spice.ai resources with Terraform
icon: cube
---

# Terraform Provider

The [Spice.ai Terraform Provider](https://registry.terraform.io/providers/spiceai/spiceai/latest) enables infrastructure-as-code management of your Spice.ai Cloud resources.

{% hint style="info" %}
Projects were previously called apps. The provider's published schema still uses the original names — the resource is `spiceai_app`, the data sources are `spiceai_app` and `spiceai_apps`, and the attribute is `app_id`. Existing configurations continue to work unchanged, and the examples below use those names.
{% endhint %}

## Quick Start

```hcl
terraform {
  required_providers {
    spiceai = {
      source  = "spiceai/spiceai"
      version = "~> 0.1"
    }
  }
}

provider "spiceai" {}

resource "spiceai_app" "app" {
  name  = "my-spicecloud-app"
  cname = "us-west-2-prod-aws-data"
}

resource "spiceai_deployment" "deploy" {
  app_id = spiceai_app.app.id
}
```

## Authentication

The provider authenticates using [OAuth 2.0 Client Credentials](../management/#id-2.-oauth-2.0-client-credentials). Create an OAuth client in the [Spice.ai Portal](https://spice.ai) under **Settings** → **OAuth Clients**.

Set credentials via environment variables (recommended):

```bash
export SPICEAI_CLIENT_ID="your-client-id"
export SPICEAI_CLIENT_SECRET="your-client-secret"
terraform plan
```

Or configure directly in the provider block:

```hcl
provider "spiceai" {
  client_id     = var.spiceai_client_id
  client_secret = var.spiceai_client_secret
}
```

### Environment Variables

| Variable                 | Description                                                        |
| ------------------------ | ------------------------------------------------------------------ |
| `SPICEAI_CLIENT_ID`      | OAuth client ID                                                    |
| `SPICEAI_CLIENT_SECRET`  | OAuth client secret                                                |
| `SPICEAI_API_ENDPOINT`   | API endpoint (default: `https://api.spice.ai`)                     |
| `SPICEAI_OAUTH_ENDPOINT` | OAuth token endpoint (default: `https://spice.ai/api/oauth/token`) |

## Resources

### spiceai\_app

Manages a Spice.ai project.

```hcl
resource "spiceai_app" "app" {
  name        = "my-spicecloud-app"
  cname       = "us-west-2-prod-aws-data"
  description = "Managed by Terraform"
  visibility  = "private"

  spicepod = <<-YAML
    version: v1
    kind: Spicepod
    name: my-spicecloud-app
    datasets:
      - name: taxi_trips
        from: s3://spiceai-demo-datasets/taxi_trips/2024/
        params:
          file_format: parquet
  YAML

  image_tag             = "latest"
  replicas              = 2
  region                = "us-east-2"
  production_branch     = "main"
  storage_claim_size_gb = 10.0
}
```

**Required arguments:**

| Argument | Type   | Description                                                                                         |
| -------- | ------ | --------------------------------------------------------------------------------------------------- |
| `name`   | string | Project name: 4–38 characters, letters, numbers, and hyphens only. Forces replacement on change.    |
| `cname`  | string | Region identifier. Get values from the `spiceai_regions` data source. Forces replacement on change. |

**Optional arguments:**

| Argument                | Type        | Description                                                         |
| ----------------------- | ----------- | ------------------------------------------------------------------- |
| `description`           | string      | Project description                                                 |
| `visibility`            | string      | `public` or `private` (default: `private`)                          |
| `spicepod`              | string      | Spicepod configuration (YAML or JSON string)                        |
| `image_tag`             | string      | Spice runtime image tag (e.g., `latest`, `v0.18.0`)                 |
| `image`                 | string      | Image name for the spiced container                                 |
| `registry`              | string      | Registry for the spiced image                                       |
| `replicas`              | number      | Number of replicas (0–10)                                           |
| `region`                | string      | Deployment region                                                   |
| `node_group`            | string      | Node group for deployment                                           |
| `storage_claim_size_gb` | number      | Persistent volume size in GB                                        |
| `production_branch`     | string      | Git branch for production deployments                               |
| `update_channel`        | string      | Update channel: `stable`, `nightly`, `internal`, `internal-sandbox` |
| `tags`                  | map(string) | Key-value tags for the project                                      |

**Read-only attributes:**

| Attribute    | Description                            |
| ------------ | -------------------------------------- |
| `id`         | Project ID                             |
| `api_key`    | Primary API key (sensitive)            |
| `cluster_id` | Cluster identifier                     |
| `created_at` | Timestamp when the project was created |

The `spicepod` can also be provided as JSON or loaded from a template file:

```hcl
# Using JSON
resource "spiceai_app" "json_config" {
  name  = "my-json-app"
  cname = "us-west-2-prod-aws-data"

  spicepod = jsonencode({
    version = "v1"
    kind    = "Spicepod"
    name    = "my-json-app"
    datasets = [{
      name = "my_dataset"
      from = "postgres://mydb/table"
    }]
  })
}

# Using a template file
resource "spiceai_app" "templated" {
  name  = "my-app"
  cname = "us-west-2-prod-aws-data"

  spicepod = templatefile("${path.module}/spicepod.yaml.tftpl", {
    app_name = "my-app"
  })
}
```

### spiceai\_deployment

Creates a deployment for a Spice.ai project. Deployments are immutable — any changes create a new deployment.

```hcl
# Basic deployment using project defaults
resource "spiceai_deployment" "deploy" {
  app_id = spiceai_app.app.id
}

# Deployment with overrides
resource "spiceai_deployment" "production" {
  app_id = spiceai_app.app.id

  image_tag      = "v0.18.0"
  replicas       = 3
  debug          = false
  branch         = "main"
  commit_sha     = "abc123def456789"
  commit_message = "Production release"
}
```

**Required arguments:**

| Argument | Type   | Description              |
| -------- | ------ | ------------------------ |
| `app_id` | string | The project ID to deploy |

**Optional arguments:**

| Argument         | Type        | Description                                            |
| ---------------- | ----------- | ------------------------------------------------------ |
| `image_tag`      | string      | Override the Spice runtime image tag                   |
| `replicas`       | number      | Override the number of replicas (0–10)                 |
| `debug`          | boolean     | Enable debug mode                                      |
| `branch`         | string      | Git branch name                                        |
| `commit_sha`     | string      | Git commit SHA                                         |
| `commit_message` | string      | Git commit message                                     |
| `triggers`       | map(string) | Map of values that force a new deployment when changed |

**Read-only attributes:**

| Attribute       | Description                                                       |
| --------------- | ----------------------------------------------------------------- |
| `id`            | Deployment ID                                                     |
| `status`        | Status: `queued`, `in_progress`, `succeeded`, `failed`, `created` |
| `created_at`    | Timestamp when the deployment was created                         |
| `started_at`    | Timestamp when the deployment started running                     |
| `finished_at`   | Timestamp when the deployment finished                            |
| `error_message` | Catalog text when the deployment failed, or when `status` is `in_progress` and Cloud is retrying a scheduling block |

Use `triggers` to automatically redeploy when project configuration changes:

```hcl
resource "spiceai_deployment" "auto" {
  app_id = spiceai_app.app.id

  triggers = {
    spicepod  = spiceai_app.app.spicepod
    image_tag = spiceai_app.app.image_tag
    replicas  = spiceai_app.app.replicas
  }
}
```

{% hint style="info" %}
Deployments are append-only. Removing a deployment resource from your configuration only removes it from Terraform state — it will not stop the running instance.
{% endhint %}

### spiceai\_secret

Manages secrets for a Spice.ai project. Secret values are encrypted at rest.

```hcl
resource "spiceai_secret" "database_password" {
  app_id = spiceai_app.app.id
  name   = "DATABASE_PASSWORD"
  value  = var.database_password
}
```

**Required arguments:**

| Argument | Type   | Description                                |
| -------- | ------ | ------------------------------------------ |
| `app_id` | number | The project ID                             |
| `name`   | string | Secret name. Forces replacement on change. |
| `value`  | string | Secret value (sensitive)                   |

**Read-only attributes:**

| Attribute    | Description                           |
| ------------ | ------------------------------------- |
| `id`         | Secret ID                             |
| `created_at` | Timestamp when the secret was created |
| `updated_at` | Timestamp when the secret was updated |

{% hint style="info" %}
After importing a secret, you must set the `value` attribute in your configuration since secret values are not returned by the API.
{% endhint %}

### spiceai\_member

Manages organization members.

```hcl
resource "spiceai_member" "developer" {
  username = "johndoe"
  roles    = ["member"]
}

resource "spiceai_member" "admin" {
  username = "janedoe"
  roles    = ["admin", "member"]
}
```

**Required arguments:**

| Argument   | Type   | Description                                         |
| ---------- | ------ | --------------------------------------------------- |
| `username` | string | Username of the user. Forces replacement on change. |

**Optional arguments:**

| Argument | Type         | Description                         |
| -------- | ------------ | ----------------------------------- |
| `roles`  | list(string) | Roles to assign (`admin`, `member`) |

**Read-only attributes:**

| Attribute    | Description                                  |
| ------------ | -------------------------------------------- |
| `id`         | Member ID (same as `user_id`)                |
| `user_id`    | User ID                                      |
| `is_owner`   | Whether the member is the organization owner |
| `created_at` | Timestamp when the member was added          |

{% hint style="warning" %}
Organization owners cannot be managed via Terraform. Attempting to modify or delete an owner will result in an error.
{% endhint %}

## Data Sources

### spiceai\_regions

Lists available deployment regions.

```hcl
data "spiceai_regions" "available" {}

# Use in project resource
resource "spiceai_app" "app" {
  name  = "my-spicecloud-app"
  cname = data.spiceai_regions.available.regions[0].cname
}
```

| Argument | Type   | Description                                      |
| -------- | ------ | ------------------------------------------------ |
| `env`    | string | Optional. Filter by environment: `prod` or `dev` |

| Attribute | Description                                                                                                  |
| --------- | ------------------------------------------------------------------------------------------------------------ |
| `default` | Default region identifier                                                                                    |
| `regions` | List of region objects with `cname`, `region`, `name`, `provider`, `provider_name`, `is_default`, `disabled` |

### spiceai\_container\_images

Lists available Spice runtime container images.

```hcl
data "spiceai_container_images" "stable" {
  channel = "stable"
}

resource "spiceai_app" "app" {
  name      = "my-spicecloud-app"
  cname     = "us-west-2-prod-aws-data"
  image_tag = data.spiceai_container_images.stable.default
}
```

| Argument  | Type   | Description                                                             |
| --------- | ------ | ----------------------------------------------------------------------- |
| `channel` | string | Optional. Release channel: `stable` or `enterprise` (default: `stable`) |

| Attribute | Description                                         |
| --------- | --------------------------------------------------- |
| `default` | Default image tag                                   |
| `images`  | List of image objects with `tag`, `name`, `channel` |

### spiceai\_api\_keys

Retrieves the API keys for a project. Each project has two API keys to support key rotation.

```hcl
data "spiceai_api_keys" "keys" {
  app_id = spiceai_app.app.id
}

output "primary_api_key" {
  value     = data.spiceai_api_keys.keys.api_key
  sensitive = true
}
```

| Argument | Type   | Description                  |
| -------- | ------ | ---------------------------- |
| `app_id` | string | **Required.** The project ID |

| Attribute   | Description                   |
| ----------- | ----------------------------- |
| `api_key`   | Primary API key (sensitive)   |
| `api_key_2` | Secondary API key (sensitive) |

### spiceai\_app

Gets details about an existing project by ID.

```hcl
data "spiceai_app" "existing" {
  id = "12345"
}

output "app_name" {
  value = data.spiceai_app.existing.name
}
```

### spiceai\_apps

Lists all projects in the organization.

```hcl
data "spiceai_apps" "all" {}

output "app_names" {
  value = [for app in data.spiceai_apps.all.apps : app.name]
}
```

### spiceai\_members

Lists all organization members.

```hcl
data "spiceai_members" "all" {}

output "member_usernames" {
  value = [for m in data.spiceai_members.all.members : m.username]
}
```

### spiceai\_secrets

Lists secrets for a project (values are masked).

```hcl
data "spiceai_secrets" "app_secrets" {
  app_id = spiceai_app.app.id
}

output "secret_names" {
  value = [for s in data.spiceai_secrets.app_secrets.secrets : s.name]
}
```

## Import

Import existing resources into Terraform state:

```bash
# Import a project by ID
terraform import spiceai_app.example 12345

# Import a deployment (app_id/deployment_id)
terraform import spiceai_deployment.example 12345/67890

# Import a secret (app_id/secret_name)
terraform import spiceai_secret.example 123/DATABASE_PASSWORD

# Import a member by user ID
terraform import spiceai_member.example 123
```

## Complete Example

```hcl
terraform {
  required_providers {
    spiceai = {
      source  = "spiceai/spiceai"
      version = "~> 0.1"
    }
  }
}

provider "spiceai" {}

# Look up available regions and images
data "spiceai_regions" "available" {}

data "spiceai_container_images" "stable" {
  channel = "stable"
}

# Create the project
resource "spiceai_app" "production" {
  name        = "my-production-app"
  description = "Production analytics project"
  visibility  = "private"
  cname       = data.spiceai_regions.available.regions[0].cname

  spicepod = <<-YAML
    version: v1
    kind: Spicepod
    name: my-production-app
    datasets:
      - name: taxi_trips
        from: s3://spiceai-demo-datasets/taxi_trips/2024/
        params:
          file_format: parquet
  YAML

  image_tag         = data.spiceai_container_images.stable.default
  replicas          = 2
  production_branch = "main"
}

# Configure secrets
resource "spiceai_secret" "database_password" {
  app_id = spiceai_app.production.id
  name   = "DATABASE_PASSWORD"
  value  = var.database_password
}

# Deploy with auto-trigger on config changes
resource "spiceai_deployment" "production" {
  app_id = spiceai_app.production.id

  triggers = {
    spicepod  = spiceai_app.production.spicepod
    image_tag = spiceai_app.production.image_tag
    replicas  = spiceai_app.production.replicas
  }
}

# Add team members
resource "spiceai_member" "team" {
  for_each = toset(["alice", "bob", "charlie"])

  username = each.key
  roles    = ["member"]
}

# Variables
variable "database_password" {
  type      = string
  sensitive = true
}

# Outputs
output "app_id" {
  value = spiceai_app.production.id
}

output "app_api_key" {
  value     = spiceai_app.production.api_key
  sensitive = true
}

output "deployment_status" {
  value = spiceai_deployment.production.status
}
```

See also:

* [Terraform Provider on Registry](https://registry.terraform.io/providers/spiceai/spiceai/latest)
* [Provider Source on GitHub](https://github.com/spiceai/terraform-provider-spiceai)
* [Management API Overview](../management/)
