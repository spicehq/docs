---
description: Configure OIDC, API keys, and identity SQL functions for Spice.ai Enterprise.
icon: lock
---

# Authentication

Spice.ai Enterprise supports multiple authentication methods that can be used independently or combined.

## OIDC (OpenID Connect)

{% hint style="info" %}
OIDC authentication is currently in **preview**.
{% endhint %}

Authenticate requests using JWT bearer tokens issued by an OIDC provider (Google, Okta, Azure AD, Auth0, etc.).

```yaml
runtime:
  auth:
    oidc:
      enabled: true
      issuer_url: https://accounts.google.com
      audience:
        - my-spice-app
      groups_claims:
        - groups
        - roles
      claims:
        user_id: sub
        org_id: "https://myapp.com/org_id"
        roles:
          - "https://myapp.com/roles"
```

### Supported JWT Algorithms

RS256, RS384, RS512, ES256, ES384, PS256, PS384, PS512, EdDSA.

JWKS keys are refreshed every 5 minutes.

### Configuration

| Parameter        | Description                                       |
| ---------------- | ------------------------------------------------- |
| `issuer_url`     | OIDC issuer URL used for JWKS discovery           |
| `audience`       | Expected `aud` claim values                       |
| `groups_claims`  | JWT claim names containing group/role information |
| `claims.user_id` | JWT claim mapped to the user identity             |
| `claims.org_id`  | JWT claim mapped to the organization identity     |
| `claims.roles`   | JWT claims mapped to role information             |

## API Keys

Authenticate requests using static API keys with `ReadOnly` or `ReadWrite` permission levels. Each key is a string; an optional `:ro` or `:rw` suffix sets its permission level.

```yaml
runtime:
  auth:
    api_key:
      enabled: true
      keys:
        - ${secrets:ro_api_key}:ro    # read-only
        - ${secrets:rw_api_key}:rw    # read-write
```

### Permission Levels

| Suffix | Level       | Description                                  |
| ------ | ----------- | -------------------------------------------- |
| `:ro`  | `ReadOnly`  | Can execute queries and read data            |
| `:rw`  | `ReadWrite` | Full access including DDL and DML operations |

A key with no suffix defaults to `ReadOnly`:

```yaml
runtime:
  auth:
    api_key:
      enabled: true
      keys:
        - ${secrets:api_key}    # ReadOnly
```

## Combined Authentication

OIDC and API keys can be used simultaneously. Requests are authenticated against either method:

```yaml
runtime:
  auth:
    oidc:
      enabled: true
      issuer_url: https://accounts.google.com
      audience:
        - my-spice-app
    api_key:
      enabled: true
      keys:
        - ${secrets:ro_api_key}:ro
```

## Protocol-Specific Authentication

| Protocol         | Method                                                       |
| ---------------- | ------------------------------------------------------------ |
| **HTTP**         | `X-API-Key` header or `Authorization: Bearer <token>` header |
| **Arrow Flight** | Handshake protocol with credentials                          |
| **gRPC**         | `x-api-key` metadata header                                  |

## Identity SQL Functions

When authentication is enabled, the following SQL functions return information about the authenticated user:

| Function                          | Description                                                          |
| --------------------------------- | -------------------------------------------------------------------- |
| `current_user_id()`               | Returns the user ID from the JWT `user_id` claim or API key identity |
| `current_org_id()`                | Returns the organization ID from the JWT `org_id` claim              |
| `current_user_has_role('<role>')` | Returns `true` if the authenticated user has the given role          |
| `session_property('<key>')`       | Returns a session property by key                                    |

These functions enable row-level security and tenant-scoped queries:

```sql
SELECT * FROM orders WHERE tenant_id = current_org_id()
```
