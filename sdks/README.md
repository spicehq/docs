---
description: Client libraries for querying Spice.ai Cloud and a local Spice runtime
icon: code
---

# SDKs

Client SDKs connect to a Spice app over [Apache Arrow Flight](../cloud/api/sql-query/apache-arrow-flight-api.md) and return results as Apache Arrow record batches, which convert cheaply into the dataframe type of each language. They are the recommended way to query Spice from application code.

Every SDK connects to either **Spice.ai Cloud** or a **local Spice runtime**. Authentication against Cloud uses an app [API key](../cloud/portal/apps/api-keys.md).

| Language            | Package                         | Latest      | Documentation                       |
| ------------------- | ------------------------------- | ----------- | ----------------------------------- |
| Python              | `spicepy`                       | `v3.1.0`    | [Python SDK](python-sdk/)           |
| TypeScript, Node.js | `@spiceai/spice`                | `3.1.0`     | [Node.js SDK](node.js-sdk/)         |
| Go                  | `github.com/spiceai/gospice/v8` | `v8.0.1`    | [Go SDK](go.md)                     |
| Rust                | `spiceai`                       | `3.0.0`     | [Rust SDK](rust-sdk/)               |
| Java                | `ai.spice:spiceai`              | `0.6.0`     | [Java SDK](java-sdk.md)             |
| C#, .NET            | `SpiceAI`                       | `0.3.0`     | [.NET SDK](dotnet-sdk.md)           |

## Default endpoints

Each SDK ships defaults for both Cloud and a local runtime. The exact values and how to override them are on each SDK's page, but the hosts are common:

| Target        | Arrow Flight                   | HTTP                      |
| ------------- | ------------------------------ | ------------------------- |
| Spice.ai Cloud | `flight.spiceai.io` port `443` | `https://data.spiceai.io` |
| Local runtime  | `localhost` port `50051`       | `http://localhost:8090`   |

{% hint style="warning" %}
Most SDKs default to the **local** runtime for Flight. Connecting to Spice.ai Cloud generally requires setting the Cloud endpoint explicitly, or calling the SDK's Cloud helper — supplying an API key alone is not always enough. Each page states what its own defaults are.
{% endhint %}

## Choosing between an SDK and the APIs directly

An SDK is preferable for application code: it handles the Flight handshake, authentication headers, and retries. Query directly against the [HTTP API](../cloud/api/sql-query/http-api.md) or [Arrow Flight API](../cloud/api/sql-query/apache-arrow-flight-api.md) when a language has no SDK, or for one-off requests from a shell.
