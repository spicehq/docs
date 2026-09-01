---
description: Client libraries for querying Spice.ai Cloud and a local Spice runtime
icon: code
---

# SDKs

Client SDKs connect to a Spice project over [Apache Arrow Flight](../cloud/api/sql-query/apache-arrow-flight-api.md) and return results as Apache Arrow record batches, which convert cheaply into the dataframe type of each language. They are the recommended way to query Spice from application code.

Every SDK connects to either **Spice.ai Cloud** or a **local Spice runtime**. Authentication against Cloud uses a project [API key](../cloud/portal/apps/api-keys.md).

| Language            | Package                         | Latest      | Documentation                       |
| ------------------- | ------------------------------- | ----------- | ----------------------------------- |
| Python              | `spicepy`                       | `v4.0.0`    | [Python SDK](python-sdk/)           |
| TypeScript, Node.js | `@spiceai/spice`                | `3.2.0`     | [Node.js SDK](node.js-sdk/)         |
| Go                  | `github.com/spiceai/gospice/v9` | `v9.0.0`    | [Go SDK](go.md)                     |
| Rust                | `spiceai`                       | `4.0.0`     | [Rust SDK](rust-sdk/)               |
| Java                | `ai.spice:spiceai`              | `0.8.0`     | [Java SDK](java-sdk.md)             |
| C#, .NET            | `SpiceAI`                       | `0.4.0`     | [.NET SDK](dotnet-sdk.md)           |

## Synchronous and asynchronous queries

Every SDK separates the two query paths by method name:

* **`sql()`** runs a query synchronously and streams Arrow record batches back on the open connection. This is the default path for querying.
* **`query()`** submits the query for asynchronous execution and returns a job handle used to poll status and fetch results once the query completes. Asynchronous queries require the runtime to be running in distributed (scheduler) mode.

| Language            | Synchronous              | Asynchronous                     |
| ------------------- | ------------------------ | -------------------------------- |
| Python              | `sql`, `sql_with_params` | `query`, `query_with_params`     |
| TypeScript, Node.js | `sql`                    | `query`, `queryWithParams`       |
| Go                  | `Sql`, `SqlWithParams`   | `Query`, `QueryWithParams`       |
| Rust                | `sql`, `sql_with_params` | `query`, `query_with_bindings`   |
| Java                | `sql`, `sqlWithParams`   | `query`, `queryWithParams`       |
| C#, .NET            | `SqlAsync`, `SqlWithParamsAsync` | `QueryAsync`, `QueryWithParamsAsync` |

{% hint style="warning" %}
In the versions listed above, `query()` submits an asynchronous job. Earlier releases of the Python, Rust, Java, Node.js, and Go SDKs used `query()` for the synchronous path, so its return type changed: it now hands back a job handle rather than a result stream. In the statically typed SDKs (Java, Rust, Go, TypeScript) existing code that assigns or iterates the result no longer compiles; in Python it fails at runtime instead. Call `sql()` for the synchronous behavior.
{% endhint %}

## Default endpoints

Each SDK ships defaults for both Cloud and a local runtime. The exact values and how to override them are on each SDK's page, but the hosts are common:

| Target        | Arrow Flight                   | HTTP                      |
| ------------- | ------------------------------ | ------------------------- |
| Spice.ai Cloud | `flight.spiceai.io` port `443` | `https://data.spiceai.io` |
| Local runtime  | `localhost` port `50051`       | `http://localhost:8090`   |

{% hint style="warning" %}
Most SDKs default to the **local** runtime for Flight. Connecting to Spice.ai Cloud generally requires setting the Cloud endpoint explicitly, or calling the SDK's Cloud helper — supplying an API key alone is not always enough. Each page states what its own defaults are.
{% endhint %}

{% hint style="info" %}
**The Cloud hosts above are region-agnostic aliases for the `us-east-1` endpoints.** They remain valid for every SDK, but they only ever reach `us-east-1`. If your project runs in another region, point the SDK at your project's own endpoint instead — the HTTP host is `https://<project-cname>.spiceai.io`, and the Flight host is the same name with `-data` replaced by `-flight`. For a project whose CNAME is `us-west-2-prod-aws-data`, that is `https://us-west-2-prod-aws-data.spiceai.io` for HTTP and `us-west-2-prod-aws-flight.spiceai.io` port `443` for Flight. Spell the Flight address the way your own SDK's page documents it — the SDKs differ, and only some accept a URI scheme: `spicepy` takes `grpc+tls://<host>`, while `gospice` takes `<host>:443` and passes anything that is not `grpc://` to gRPC unchanged. The portal shows the CNAME for each project.

`spicepy` v4.0.0 sets `config.DEFAULT_FLIGHT_URL` / `DEFAULT_HTTP_URL` to the explicit `us-east-1` hostnames for this reason; passing the region-agnostic names to any SDK still works.
{% endhint %}

## Choosing between an SDK and the APIs directly

An SDK is preferable for application code: it handles the Flight handshake, authentication headers, and retries. Query directly against the [HTTP API](../cloud/api/sql-query/http-api.md) or [Arrow Flight API](../cloud/api/sql-query/apache-arrow-flight-api.md) when a language has no SDK, or for one-off requests from a shell.
