---
icon: code
description: Spice.ai APIs, SDKs, endpoints, and query best practices.
---

# APIs & SDKs

## API Endpoints

Spice.ai exposes two sets of APIs: **runtime APIs** (for querying data and AI) and the **Management API** (for managing projects and infrastructure).

### Runtime APIs

| API                | Endpoint                                      | Auth                  | Documentation                                                   |
| ------------------ | --------------------------------------------- | --------------------- | --------------------------------------------------------------- |
| SQL (HTTP)         | `https://data.spiceai.io/v1/sql`              | `X-API-Key` header    | [HTTP API](../cloud/api/sql-query/http-api.md)                        |
| SQL (Arrow Flight) | `grpc+tls://flight.spiceai.io`                | Password in handshake | [Arrow Flight API](../cloud/api/sql-query/apache-arrow-flight-api.md) |
| LLM Chat           | `https://data.spiceai.io/v1/chat/completions` | `X-API-Key` header    | [LLM API](../cloud/api/openai-api.md)                                 |
| Search             | `https://data.spiceai.io/v1/search`           | `X-API-Key` header    | [Search API](../cloud/api/search.md)                                  |
| Health             | `https://data.spiceai.io/health`              | None                  | [Health API](../cloud/api/health.md)                                  |

### Management API

The Management API at `https://api.spice.ai/v1/` uses **personal access tokens** or **OAuth tokens** (not project API keys).

Projects, deployments, API keys, secrets, and organization members are all managed through it. Every operation is listed in the [Management API reference](../cloud/api/management/), which renders the published OpenAPI specification.

## SDKs

Official SDKs handle authentication, serialization, and connection management for you.

| Language | Package                      | Documentation                       |
| -------- | ---------------------------- | ----------------------------------- |
| Python   | `spicepy`                    | [Python SDK](../sdks/python-sdk/)   |
| Node.js  | `@spiceai/spiceai`           | [Node.js SDK](../sdks/node.js-sdk/) |
| Go       | `github.com/spiceai/gospice` | [Go SDK](../sdks/go.md)             |
| Rust     | `spiceai`                    | [Rust SDK](../sdks/rust-sdk/)       |
| Java     |                              | [Java SDK](../sdks/java-sdk.md)     |
| .NET     |                              | [Dotnet SDK](../sdks/dotnet-sdk.md) |

### Quick example (Python)

```python
from spicepy import Client

client = Client("YOUR_API_KEY")
reader = client.query("SELECT * FROM my_table LIMIT 10")
df = reader.read_pandas()
print(df)
```

## Choosing HTTP vs. Arrow Flight

|                 | HTTP API                      | Arrow Flight API                     |
| --------------- | ----------------------------- | ------------------------------------ |
| **Format**      | JSON                          | Apache Arrow (binary)                |
| **Best for**    | Simple queries, small results | Large datasets, production workloads |
| **Row limits**  | Yes                           | No                                   |
| **Streaming**   | No                            | Yes                                  |
| **Performance** | Good                          | Best                                 |
| **SDK support** | All SDKs                      | All SDKs                             |

**Recommendation**: Use Arrow Flight (via SDKs) for production workloads and large result sets. Use the HTTP API for quick testing, small queries, or REST-based integrations.

## Query Best Practices

### Use `LIMIT` and `OFFSET` for large results

```sql
SELECT * FROM my_table
ORDER BY id
LIMIT 1000 OFFSET 0
```

Increment `OFFSET` to page through results. Always include `ORDER BY` for deterministic pagination.

### Use recent tables for near-real-time data

Recent tables provide fast access to the last ~30 minutes of data, ideal for dashboards and monitoring.

### Combine SQL with client-side processing

Use SQL for filtering, aggregation, and joins, then use client libraries (pandas, NumPy, etc.) for further processing:

```python
reader = client.query("SELECT date, value FROM metrics WHERE date > '2025-01-01'")
df = reader.read_pandas()
df['rolling_avg'] = df['value'].rolling(7).mean()
```

### Use data acceleration for repeated queries

Enable [data acceleration](../features/data-acceleration/) on frequently queried datasets to avoid hitting the source on every request.

## Common Issues

### Arrow Flight TLS errors

Set the `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` environment variable:

```bash
# macOS
export GRPC_DEFAULT_SSL_ROOTS_FILE_PATH=/etc/ssl/cert.pem
```

### HTTP API returns truncated results

The HTTP API has built-in row and timeout limits. Switch to Arrow Flight or an SDK for unlimited streaming results.

### Management API returns `401`

The Management API uses **personal access tokens**, not project API keys. Generate a token under [Profile → Personal Access Tokens](../cloud/portal/profile/personal-access-tokens.md).

## Further Reading

- [SQL Query API](../cloud/api/sql-query/)
- [LLM API](../cloud/api/openai-api.md)
- [Search API](../cloud/api/search.md)
- [Best Practices](../cloud/best-practices.md)
- [SQL Reference](../cloud/reference/sql-reference.md)
- [Arrow Flight Samples](../cloud/reference/arrow-flight-samples.md)
