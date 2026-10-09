---
description: Spice.ai Cloud Platform API Reference
icon: terminal
---

# API Reference

The Spice.ai Cloud Platform exposes two sets of APIs: **Runtime APIs** for querying data and AI, and the **Management API** for managing projects and infrastructure.

## Runtime APIs

Runtime APIs are served at `https://data.spiceai.io` and authenticated with [Project API keys](../portal/apps/api-keys.md).

| API                      | Endpoint                       | Documentation                                            |
| ------------------------ | ------------------------------ | -------------------------------------------------------- |
| SQL Query (HTTP)         | `POST /v1/sql`                 | [HTTP API](sql-query/http-api.md)                        |
| SQL Query (Arrow Flight) | `grpc+tls://flight.spiceai.io` | [Arrow Flight API](sql-query/apache-arrow-flight-api.md) |
| LLM Chat Completions     | `POST /v1/chat/completions`    | [LLM API](openai-api.md)                                 |
| Text-to-SQL              | `POST /v1/nsql`                | [Text-to-SQL API](nsql.md)                               |
| Text-to-SQL context      | `GET /v1/nsql/context`         | [Text-to-SQL API](nsql.md)                               |
| Search                   | `POST /v1/search`              | [Search API](search.md)                                  |
| MCP                      | `POST /v1/mcp`                 | [MCP API](mcp.md)                                        |
| Health                   | `GET /health`                  | [Health API](health.md)                                  |
| Metrics                  | `GET /v1/metrics`              | [Metrics API](metrics.md)                                |

## Management API

The Management API is served at `https://api.spice.ai` and authenticated with [Personal Access Tokens](../portal/profile/personal-access-tokens.md) or OAuth client credentials.

| Endpoint         | Documentation                                              |
| ---------------- | ---------------------------------------------------------- |
| Projects         | [Projects API](management/)                                |
| Deployments      | [Deployments API](/broken/pages/cW4Y9zvF1YF9X2ExU15D)      |
| Secrets          | [Secrets API](/broken/pages/jux7LfeRfZnBFKMpjIXA)          |
| API Keys         | [API Keys API](/broken/pages/C2SEPG58kdQqhs4SL9B7)         |
| Members          | [Members API](/broken/pages/fDcgKtae3y2pEzLWtbVg)          |
| Regions          | [Regions API](/broken/pages/6ZPPX3ncuyaq7usBYCzO)          |
| Container Images | [Container Images API](/broken/pages/5fsccwHHHi12wJt5s0Ca) |
| Health           | [Health API](/broken/pages/MMiAVKRYaydEPCc1zdZU)           |

See the full [Management API reference](management/) for authentication, scopes, rate limits, and examples.

## OpenAPI Specification

The Spice.ai Platform API spec is available at:

```
https://api.spice.ai/v1/docs/openapi.json
```
