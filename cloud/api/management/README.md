---
description: Management and Deployment API documentation for api.spice.ai
icon: code
---

# Management APIs

The Spice.ai Management API (also known as the control-plane API) provides programmatic access to manage Spice.ai Cloud resources—projects, deployments, secrets, API keys, and organization members.

{% hint style="info" %}
**Projects were previously called apps.** Every `/v1/projects` endpoint is also served at the legacy `/v1/apps` path, which remains supported. Existing integrations continue to work without changes.

The legacy paths are marked deprecated in the OpenAPI specification, and new integrations should use `/v1/projects`. See [Projects and apps](#projects-and-apps).
{% endhint %}

## Base URL

```
https://api.spice.ai
```

## API Version

All API endpoints are versioned under `/v1`:

```
https://api.spice.ai/v1
```

## Projects and apps

What the API and portal now call a **project** was previously called an **app**. The resource is unchanged — only the name is different.

Both path prefixes reach the same handlers:

| Path                     | Status                | List response envelope |
| ------------------------ | --------------------- | ---------------------- |
| `/v1/projects`           | Canonical             | `{ "projects": [...] }` |
| `/v1/apps`               | Legacy, still served   | `{ "apps": [...] }`     |

Two details matter when migrating:

* **The list envelope differs.** `GET /v1/projects` returns results under a `projects` key, while `GET /v1/apps` keeps its original `apps` key. A client switching to the canonical path must read the new key. All other response shapes and field names are identical, including the `id` and `name` fields on each resource.
* **OAuth scope names are unchanged.** The scopes are still `apps:read`, `apps:write`, and `apps:delete`, because they are embedded in already-issued tokens. They grant access to projects under either path.

The Spice CLI, Terraform provider, and SDKs continue to call the legacy paths and are unaffected.

## Authentication

The Management API supports three authentication methods:

### 1. Personal Access Tokens (PATs)

PATs are long-lived, user-scoped tokens. Recommended for:

* CLI tools and automation scripts
* Personal integrations

**Creating a PAT:**

1. Sign in to [Spice.ai Cloud Portal](https://spice.ai)
2. Navigate to **Profile** → **Personal Access Tokens**
3. Click **Create Token**
4. Select an organization and configure scopes
5. Copy the token (it won't be shown again)

**Using a PAT:**

```bash
curl -H "Authorization: Bearer <your-pat-token>" \
  https://api.spice.ai/v1/projects
```

Learn more: [Personal Access Tokens](../../portal/profile/personal-access-tokens.md)

### 2. OAuth 2.0 Client Credentials

OAuth client credentials are organization-scoped tokens. Ideal for:

* CI/CD pipelines
* Service-to-service authentication
* Multi-tenant applications
* Third-party integrations

**Step 1 — Exchange client credentials for an access token:**

```bash
curl -X POST https://spice.ai/api/oauth/token \
  -H "Content-Type: application/json" \
  -d '{
    "client_id": "your-client-id",
    "client_secret": "your-client-secret",
    "grant_type": "client_credentials"
  }'
```

The response contains an `access_token`:

```json
{
  "access_token": "eyJhbGciOiJSUzI...",
  "token_type": "Bearer",
  "expires_in": 3600
}
```

**Step 2 — Use the access token in subsequent requests:**

```bash
curl -H "Authorization: Bearer <access-token>" \
  https://api.spice.ai/v1/projects
```

### 3. User Session Tokens (CLI)

The Spice CLI stores a credential for the Management API with `spice login`. On a terminal it asks which one to use:

```bash
spice login
? How would you like to authenticate to Spice Cloud? ›
❯ Login with a web browser
  Paste an access token
```

**Login with a web browser** signs in through the browser and stores the resulting user session token, which carries the user's identity. **Paste an access token** stores a personal access token instead.

The prompt appears only when the CLI is attached to a terminal. Scripts and CI pipelines name the method as a subcommand, which also lets them supply the credential without being prompted for it:

| Command                    | Credential stored          | Supplying it without a prompt                                                                                |
| -------------------------- | -------------------------- | ------------------------------------------------------------------------------------------------------------ |
| `spice login subscription` | User session token         | `--device` prints the URL and a one-time code to enter on another device instead of opening a browser, for SSH and headless shells |
| `spice login token`        | Personal access token      | `--token`, or the `SPICE_CLOUD_PAT` environment variable                                                       |
| `spice login api`          | OAuth client credentials   | `--client-id` and `--client-secret`, or `SPICE_CLOUD_CLIENT_ID` and `SPICE_CLOUD_CLIENT_SECRET`                |

Run with no terminal and no subcommand, `spice login` reports that the login type must be chosen explicitly rather than waiting on a prompt that cannot be answered.

## Organization context

Every Management API request acts on exactly one organization. By default that is the organization the credential was minted against. Send the `X-Org-Name` header with an organization handle to act on a different one.

| Credential                            | Organizations it can act on                     |
| ------------------------------------- | ----------------------------------------------- |
| Personal access token or CLI session  | Any organization the token owner belongs to      |
| OAuth client credentials              | Only the organization the client was issued to   |

A user credential carries a user identity, so the API authorizes the request against that user's membership. A machine credential carries no user identity and stays pinned to one organization: a header naming a different organization is refused rather than ignored.

{% hint style="info" %}
**Reach never widens on its own.** Without `X-Org-Name`, a credential acts on the organization it was minted against. Reaching another organization requires the client to send the header, and the caller to hold the membership, role, and scope the action needs.
{% endhint %}

Handles are matched case-insensitively and may contain letters, numbers, dots, hyphens, and underscores.

```bash
curl -H "Authorization: Bearer <token>" \
  -H "X-Org-Name: acme-corp" \
  https://api.spice.ai/v1/projects
```

### Discovering organizations

`GET /v1/orgs` lists the organizations the caller belongs to, with the caller's role in each. Use it to choose a value for `X-Org-Name`.

```bash
curl -H "Authorization: Bearer <token>" \
  https://api.spice.ai/v1/orgs
```

```json
{
  "orgs": [
    { "id": 42, "name": "acme-corp", "role": "owner" },
    { "id": 57, "name": "example-org", "role": "member" }
  ]
}
```

* `name` is the organization handle — the value `X-Org-Name` accepts, and the one used in `<org>/<project>` addressing.
* `role` is the caller's highest-privilege role in that organization: `owner`, `admin`, `member`, or `viewer`.
* A caller who belongs to no organization receives `200` with an empty `orgs` array, never a `404`.
* An OAuth client credential lists only the organization it was issued to, with the role `owner`.
* Requires the `apps:read` scope.

### Refused requests

A refusal returns a machine-readable `code` alongside `error`, because each one has a different remedy:

| Code                     | Status | Meaning                                                          | Remedy                                          |
| ------------------------ | ------ | ---------------------------------------------------------------- | ----------------------------------------------- |
| `org_forbidden`          | `403`  | The named organization does not exist or is not visible to the caller | Verify the handle; if it is correct, ask an organization owner for an invitation |
| `forbidden`              | `403`  | The caller is a member but lacks the role the action needs        | Ask an owner or admin for a higher role          |
| `insufficient_scope`     | `403`  | The credential was not granted the required scope                 | Reissue the credential with the scope            |
| `org_assertion_mismatch` | `403`  | A machine credential named an organization other than its own     | Use a credential issued for that organization    |
| `invalid_org_assertion`  | `400`  | The header value is not a valid organization handle               | Correct the handle                               |

Two details are easy to miss:

* **A blank `X-Org-Name` is an error, not an omission.** It returns `invalid_org_assertion`, because a client that sent the header believes it named an organization.
* **An organization that does not exist and one the caller cannot see both return `org_forbidden`.** The header cannot be used to test whether an organization exists.

## OAuth Scopes

Access to API resources is controlled through scopes. PATs and OAuth clients must be granted appropriate scopes:

| Scope               | Description                                                   |
| ------------------- | ------------------------------------------------------------- |
| `*`                 | Full access to all resources (not recommended for production) |
| `apps:read`         | Read project information                                      |
| `apps:write`        | Create and update projects                                    |
| `apps:delete`       | Delete projects                                               |
| `deployments:read`  | View deployment status and history                            |
| `deployments:write` | Create new deployments                                        |
| `secrets:read`      | List and view secrets (values are masked)                     |
| `secrets:write`     | Create, update, and delete secrets                            |
| `config:read`       | Read project configuration                                    |
| `config:write`      | Update project configuration                                  |
| `members:read`      | View organization members                                     |
| `members:write`     | Add and update organization members                           |
| `members:delete`    | Remove organization members                                   |

**Scope hierarchy:**

* A write scope automatically includes its corresponding read scope (e.g. `apps:write` implies `apps:read`).
* The wildcard scope (`*`) grants all permissions.
* The `apps:*` scope names are unchanged by the projects rename, and apply to projects.

## Rate Limiting

Requests are rate-limited per project. These limits are a high-level failsafe; actual throughput depends on the size of your deployed Spice instance or cluster.

### Per-Project Request Rate Limits

| Plan       | Requests / second |
| ---------- | ----------------- |
| Community  | 100               |
| Developer  | 1,000             |
| Pro Teams  | 10,000            |
| Enterprise | 100,000           |

### Concurrent Query Limits

These limits apply to SQL queries executed against your Spice runtime, not to management API calls.

| Plan       | Concurrent Queries | Query Timeout |
| ---------- | ------------------ | ------------- |
| Developer  | 16                 | 90 seconds    |
| Pro Teams  | 64                 | 5 minutes     |
| Enterprise | 1,024              | 30 minutes    |

## Error Responses

The API uses standard HTTP status codes:

| Status Code                 | Description                             |
| --------------------------- | --------------------------------------- |
| `200 OK`                    | Request succeeded                       |
| `201 Created`               | Resource created successfully           |
| `202 Accepted`              | Request accepted (async operation)      |
| `204 No Content`            | Request succeeded with no response body |
| `400 Bad Request`           | Invalid request body or parameters      |
| `401 Unauthorized`          | Missing or invalid authentication       |
| `403 Forbidden`             | Insufficient scope or permissions       |
| `404 Not Found`             | Resource not found                      |
| `409 Conflict`              | Resource already exists or conflict     |
| `429 Too Many Requests`     | Rate limit exceeded                     |
| `500 Internal Server Error` | Server error                            |

**Error Response Format:**

```json
{
  "error": "Human-readable error message",
  "details": {
    "fieldErrors": {
      "field_name": ["Validation error message"]
    }
  }
}
```

Authorization failures also carry a machine-readable `code`, so a client can tell the refusals apart instead of guessing:

```json
{
  "error": "You are not a member of the requested organization",
  "code": "org_forbidden"
}
```

See [Refused requests](#refused-requests) for the codes and their remedies.

## Pagination

List endpoints support cursor-based pagination with the following query parameters:

| Parameter | Type    | Default | Description                                  |
| --------- | ------- | ------- | -------------------------------------------- |
| `limit`   | integer | 20      | Maximum number of items to return (max: 100) |
| `offset`  | integer | 0       | Number of items to skip                      |

## OpenAPI Specification

The API publishes its own OpenAPI specification, which describes every endpoint and the headers it accepts:

```
https://api.spice.ai/openapi.json
```

The same document is also served at `https://api.spice.ai/v1/docs` and `https://api.spice.ai/v1/docs/openapi.json`.

## SDK Support

Official SDKs are available for popular languages:

* [Python SDK](../../../sdks/python-sdk/)
* [Node.js SDK](../../../sdks/node.js-sdk)
* [Go SDK](../../../sdks/go.md)
* [Rust SDK](../../../sdks/rust-sdk/)

## Endpoints

* [Health](/broken/pages/MMiAVKRYaydEPCc1zdZU) - API health check
* [Regions](/broken/pages/6ZPPX3ncuyaq7usBYCzO) - List available deployment regions
* [Projects](/broken/pages/Cxualhhbj3JVjFycQplA) - Manage Spice projects
* [Deployments](/broken/pages/cW4Y9zvF1YF9X2ExU15D) - Deploy and manage project deployments
* [Secrets](/broken/pages/jux7LfeRfZnBFKMpjIXA) - Manage project secrets
* [API Keys](/broken/pages/C2SEPG58kdQqhs4SL9B7) - Manage project API keys
* [Members](/broken/pages/fDcgKtae3y2pEzLWtbVg) - Manage organization members
* [Organizations](#discovering-organizations) - List the organizations the caller belongs to
* [Metrics](../metrics.md) - Scrape per-project runtime metrics
* [Container Images](/broken/pages/5fsccwHHHi12wJt5s0Ca) - List available runtime versions

## Terraform Provider

Manage Spice.ai resources as infrastructure-as-code with the [Spice.ai Terraform Provider](../management-api/terraform.md). See the [Terraform Provider](../management-api/terraform.md) page for resources, data sources, import instructions, and complete examples.

## Examples

### List all projects

```bash
curl -H "Authorization: Bearer <token>" \
  https://api.spice.ai/v1/projects
```

Results are returned under a `projects` key. The legacy `GET /v1/apps` path returns the same records under an `apps` key.

### Create a new project

```bash
curl -X POST https://api.spice.ai/v1/projects \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "my-project",
    "region": "us-west-2",
    "description": "My Spice project",
    "visibility": "private"
  }'
```

Organizations with a [dedicated cluster](dedicated-clusters.md) can pass `cluster_name` in place of `region` to create the project on their dedicated infrastructure.

{% hint style="info" %}
**Project name requirements.** `name` must be 4–38 characters and contain only letters, numbers, and hyphens. A name outside those bounds is rejected with `400`.

Names are unique within an organization and are compared case-insensitively, so a name that differs from an existing project only by case is rejected with `409`.
{% endhint %}

### Create a deployment

```bash
curl -X POST https://api.spice.ai/v1/projects/123/deployments \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "branch": "main",
    "commit_sha": "abc123",
    "commit_message": "Deploy latest changes"
  }'
```

### Add a secret

```bash
curl -X POST https://api.spice.ai/v1/projects/123/secrets \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "DATABASE_URL",
    "value": "postgresql://user:pass@host:5432/db"
  }'
```

## Support

Have questions or running into issues?

* [GitHub Issues](https://github.com/spicehq/spiceai/issues)
* [Community Discord](https://discord.gg/spiceai)
* [Support](../../../support/support.md)
