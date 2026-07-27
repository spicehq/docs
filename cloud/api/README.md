---
description: Spice.ai Cloud Platform API Reference
icon: terminal
---

# API Reference

The Spice.ai Cloud Platform exposes two sets of APIs: **Runtime APIs** for querying data and AI, and the **Management API** for managing apps and infrastructure.

## Runtime APIs

Runtime APIs are served at `https://data.spiceai.io` and authenticated with [App API keys](/docs/portal/apps/api-keys).

| API                      | Endpoint                       | Documentation                                            |
| ------------------------ | ------------------------------ | -------------------------------------------------------- |
| SQL Query (HTTP)         | `POST /v1/sql`                 | [HTTP API](sql-query/http-api.md)                        |
| SQL Query (Arrow Flight) | `grpc+tls://flight.spiceai.io` | [Arrow Flight API](sql-query/apache-arrow-flight-api.md) |
| LLM Chat Completions     | `POST /v1/chat/completions`    | [LLM API](openai-api.md)                                 |
| Text-to-SQL              | `POST /v1/nsql`                | [Text-to-SQL API](nsql.md)                               |
| Text-to-SQL context      | `GET /v1/nsql/context`         | [Text-to-SQL API](nsql.md)                               |
| Search                   | `POST /v1/search`              | [Search API](search.md)                                  |
| Health                   | `GET /health`                  | [Health API](health.md)                                  |
| Metrics                  | `GET /v1/metrics`              | [Metrics API](metrics.md)                                |

## Management API

The Management API is served at `https://api.spice.ai` and authenticated with [Personal Access Tokens](/docs/portal/profile/personal-access-tokens) or OAuth client credentials.

| Endpoint         | Documentation                                              |
| ---------------- | ---------------------------------------------------------- |
| Apps             | [Apps API](/api/management-api/management/apps)             |
| Deployments      | [Deployments API](/api/management-api/management/deployments)      |
| Secrets          | [Secrets API](/api/management-api/management/secrets)          |
| API Keys         | [API Keys API](/api/management-api/management/api-keys)         |
| Members          | [Members API](/api/management-api/management/members)          |
| Regions          | [Regions API](/api/management-api/management/regions)          |
| Container Images | [Container Images API](/docs/portal/app-spicepod/spice-runtime-versions) |
| Health           | [Health API](/api/management-api/management/health)           |

See the full [Management API reference](management/) for authentication, scopes, rate limits, and examples.

## OpenAPI Specification

The Spice.ai Platform API spec is available at:

```
https://api.spice.ai/v1/docs/openapi.json
```
