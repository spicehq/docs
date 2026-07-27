---
description: Management and Deployment API documentation for api.spice.ai
icon: code
---

# Management APIs

The Spice.ai Management API (also known as the control-plane API) provides programmatic access to manage Spice.ai Cloud resources—apps, deployments, secrets, API keys, and organization members.

## Base URL

```
https://api.spice.ai
```

## API Version

All API endpoints are versioned under `/v1`:

```
https://api.spice.ai/v1
```

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
  https://api.spice.ai/v1/apps
```

Learn more: [Personal Access Tokens](/docs/portal/profile/personal-access-tokens)

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
  https://api.spice.ai/v1/apps
```

### 3. User Session Tokens (CLI)

The Spice CLI obtains a user session token through the browser-based login flow. This token grants full access to resources in your personal organization.

```bash
spice login  # Opens a browser to authenticate
```

## OAuth Scopes

Access to API resources is controlled through scopes. PATs and OAuth clients must be granted appropriate scopes:

| Scope               | Description                                                   |
| ------------------- | ------------------------------------------------------------- |
| `*`                 | Full access to all resources (not recommended for production) |
| `apps:read`         | Read app information                                          |
| `apps:write`        | Create and update apps                                        |
| `apps:delete`       | Delete apps                                                   |
| `deployments:read`  | View deployment status and history                            |
| `deployments:write` | Create new deployments                                        |
| `secrets:read`      | List and view secrets (values are masked)                     |
| `secrets:write`     | Create, update, and delete secrets                            |
| `config:read`       | Read app configuration                                        |
| `config:write`      | Update app configuration                                      |
| `members:read`      | View organization members                                     |
| `members:write`     | Add and update organization members                           |
| `members:delete`    | Remove organization members                                   |

**Scope hierarchy:**

* A write scope automatically includes its corresponding read scope (e.g. `apps:write` implies `apps:read`).
* The wildcard scope (`*`) grants all permissions.

## Rate Limiting

Requests are rate-limited per app. These limits are a high-level failsafe; actual throughput depends on the size of your deployed Spice instance or cluster.

### Per-App Request Rate Limits

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

## Pagination

List endpoints support cursor-based pagination with the following query parameters:

| Parameter | Type    | Default | Description                                  |
| --------- | ------- | ------- | -------------------------------------------- |
| `limit`   | integer | 20      | Maximum number of items to return (max: 100) |
| `offset`  | integer | 0       | Number of items to skip                      |

## OpenAPI Specification

Browse the interactive API reference at `https://api.spice.ai/v1/docs`, or download the OpenAPI spec:

```
https://api.spice.ai/v1/docs/openapi.json
```

## SDK Support

Official SDKs are available for popular languages:

* [Python SDK](/sdks/sdks/python-sdk)
* [Node.js SDK](/sdks/sdks/node.js-sdk)
* [Go SDK](/sdks/sdks/go)
* [Rust SDK](/sdks/sdks/rust-sdk)

## Endpoints

* [Health](/api/management-api/management/health) - API health check
* [Regions](/api/management-api/management/regions) - List available deployment regions
* [Apps](/api/management-api/management/apps) - Manage Spice apps
* [Deployments](/api/management-api/management/deployments) - Deploy and manage app deployments
* [Secrets](/api/management-api/management/secrets) - Manage app secrets
* [API Keys](/api/management-api/management/api-keys) - Manage app API keys
* [Members](/api/management-api/management/members) - Manage organization members
* [Metrics](../metrics.md) - Scrape per-app runtime metrics
* [Container Images](/docs/portal/app-spicepod/spice-runtime-versions) - List available runtime versions

## Terraform Provider

Manage Spice.ai resources as infrastructure-as-code with the [Spice.ai Terraform Provider](../management-api/terraform.md). See the [Terraform Provider](../management-api/terraform.md) page for resources, data sources, import instructions, and complete examples.

## Examples

### List all apps

```bash
curl -H "Authorization: Bearer <token>" \
  https://api.spice.ai/v1/apps
```

### Create a new app

```bash
curl -X POST https://api.spice.ai/v1/apps \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "my-app",
    "region": "us-west-2",
    "description": "My Spice app",
    "visibility": "private"
  }'
```

Organizations with a [dedicated cluster](dedicated-clusters.md) can pass `cluster_name` in place of `region` to create the app on their dedicated infrastructure.

### Create a deployment

```bash
curl -X POST https://api.spice.ai/v1/apps/123/deployments \
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
curl -X POST https://api.spice.ai/v1/apps/123/secrets \
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
* [Support](/help/support)
