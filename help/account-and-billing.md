---
icon: user-gear
description: Manage your Spice.ai account, API keys, organizations, and billing.
---

# Account & Billing

## Signing Up

Spice.ai Cloud uses **GitHub for authentication**. To create an account:

1. Go to [spice.ai](https://spice.ai).
2. Click **Sign in with GitHub** and authorize the Spice.ai GitHub App.
3. A personal organization is created automatically for you.

See the full [Getting Started guide](../cloud/getting-started/get-started/) for a step-by-step walkthrough.

## API Keys

Each Spice.ai project has **two API keys** that authenticate requests to the data and AI APIs.

### Where to find your API keys

Navigate to your project in the [Portal](https://spice.ai) → **Settings** → **API Keys**.

### How API keys are used

| API                                                             | Authentication        |
| --------------------------------------------------------------- | --------------------- |
| [SQL HTTP API](../cloud/api/sql-query/http-api.md)                    | `X-API-Key` header    |
| [Arrow Flight API](../cloud/api/sql-query/apache-arrow-flight-api.md) | Password in handshake |
| [LLM API](../cloud/api/openai-api.md)                                 | `X-API-Key` header    |
| [Search API](../cloud/api/search.md)                                  | `X-API-Key` header    |
| [SDKs](../sdks/python-sdk/)                                     | Client configuration  |

### Rotating API keys

Each project has two keys to support **zero-downtime rotation**:

1. Regenerate Key 2 (while clients use Key 1).
2. Update all clients to use Key 2.
3. Regenerate Key 1.

{% hint style="warning" %}
Regenerating a key **immediately invalidates** the old key. Ensure all clients are updated before regenerating.
{% endhint %}

API keys can also be managed via the [Management API](../cloud/api/management/).

## Personal Access Tokens

The [Management API](../cloud/api/management/) uses **personal access tokens** (not project API keys) for authentication. Generate tokens in the Portal under [Profile → Personal Access Tokens](../cloud/portal/profile/personal-access-tokens.md).

## Organizations

Organizations are the primary unit for managing teams, projects, billing, and access.

### Personal organization

Every user automatically receives a **personal organization** on signup. This is a single-member org tied to your account.

### Team organizations

Team organizations allow multiple members to collaborate on shared projects. To create one:

1. In the Portal, go to **Organizations** → **Create Organization**.
2. Connect a **GitHub organization** to link team membership.

### Managing members

Organization admins can add members by:

- **Spice username**
- **GitHub username** (requires the user to have a public email on their GitHub profile)
- **Email invitation**

To remove a member, go to your organization's **Settings** → **Members**.

See [Organizations](../cloud/portal/organizations.md) for full details.

## Plans & Billing

### Community Plan (Free)

The free [Community Plan](../cloud/pricing/community.md) includes:

- One project per organization.
- Access to all building blocks (data connectors, AI gateway, search, etc.).
- **Auto-pause**: Projects without API requests for 7 days are automatically paused. Paused projects can be restored anytime in the Portal.

### Paid Plans

[Paid plans](../cloud/pricing/plans.md) provide:

- Higher request and query limits.
- No auto-pause.
- Service-level guarantees.
- Priority support with SLA.
- SOC 2 Type II report access.

See [Pricing](https://spice.ai/pricing/cloud) for current plan details.

## Secrets

Project secrets store sensitive configuration values (API keys for data sources, model provider credentials, etc.).

### Key behaviors

- Secrets are **project-scoped** and encrypted.
- Values are **write-only** — they cannot be retrieved after saving.
- To change a value: delete the secret and recreate it.
- Secret changes require a **new deployment** to take effect.
- Reference secrets in your Spicepod with: `${secrets:SECRET_NAME}`.

See [Secrets](../cloud/portal/apps/secrets.md) for details.

## Security & Compliance

Spice.ai Cloud is **SOC 2 Type II** compliant. Key security measures include:

- Authentication and authorization with RBAC.
- MFA enforcement.
- Encryption in transit (TLS 1.2+) and at rest.
- Auditable access logs.
- Secure code scanning and third-party audits.

See [Security](../security/security.md) for the full security overview. Enterprise plan customers can request the SOC 2 report.
