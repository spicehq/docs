---
description: Dotnet SDK for Spice.ai
icon: code
---

# Dotnet SDK

The [Dotnet SDK](https://github.com/spiceai/spice-dotnet) `SpiceAI` is the easiest way to query [Spice.ai](https://spice.ai) from Dotnet.

It uses [Apache Arrow Flight](https://arrow.apache.org/docs/format/Flight.html) to efficiently stream data to the client and [Apache Arrow](https://arrow.apache.org/) Records as data frames.

### Requirements

The package targets .NET Standard 2.0, .NET 8.0, .NET 9.0, and .NET 10.0. .NET 8.0 or later is recommended.

### Installation

Add Spice SDK

```bash
dotnet add package SpiceAI
```

### Usage

1. Create a `SpiceClient` by providing your API key to `SpiceClientBuilder`. Get your free API key at [Spice.ai](https://spice.ai/).

`SpiceClient` implements `IDisposable`, so declare it with `using`.

```csharp
using Spice;

using var client = new SpiceClientBuilder()
    .WithSpiceCloud("API_KEY")
    .Build();
```

{% hint style="warning" %}
`WithSpiceCloud` takes the **API key**, not a URL, and applies the Spice.ai Cloud endpoints itself. The key must be in `appId|key` form. To connect to a custom endpoint, use `WithFlightAddress` instead.
{% endhint %}

The builder also accepts `WithApiKey(string)`, `WithFlightAddress(string)`, `WithHttpAddress(string)`, `WithMaxRetries(int)` (default 3), `WithUserAgent(string)`, and `WithTls(bool)`.

2. Execute a query and get back an Apache Arrow [Flight Client Record Batch Stream Reader](https://github.com/apache/arrow/blob/67bbf846d0d47075e1711b3fb4cf8fb05c74bd09/csharp/src/Apache.Arrow.Flight/Client/FlightClientRecordBatchStreamReader.cs#L22).

```csharp
var result = await client.SqlAsync("SELECT * FROM tpch.lineitem LIMIT 10;");
```

3. Iterate through the reader to access the records.

```csharp
var enumerator = result.GetAsyncEnumerator();
while (await enumerator.MoveNextAsync())
{
    var batch = enumerator.Current;
    // Process batch
}
```

### Parameterized queries

`SqlWithParamsAsync(string sql, params object?[] parameters)` binds positional `$1`, `$2` placeholders:

```csharp
var data = await client.SqlWithParamsAsync(
    "SELECT * FROM tpch.lineitem WHERE l_quantity > $1 LIMIT 10;", 10);

var batch = await data!.ReadNextRecordBatchAsync();
```

{% hint style="info" %}
Parameters are positional only. Named placeholders such as `:product_id` are not supported.
{% endhint %}

### Asynchronous queries

`QueryAsync(string sql)` and `QueryWithParamsAsync(string sql, params object?[] parameters)` submit a query for asynchronous execution and return an `AsyncQuery` handle instead of a reader. They require the runtime to be running in distributed (scheduler) mode.

```csharp
var query = await client.QueryAsync("SELECT * FROM taxi_trips");
var stream = await query.GetResultsAsync(); // waits for completion
```

The handle also exposes `Id`, `GetStatusAsync()`, `WaitAsync()`, and `CancelAsync()`.

### Usage with local Spice runtime

Follow the [quickstart guide](https://github.com/spiceai/spiceai?tab=readme-ov-file#%EF%B8%8F-quickstart-local-machine) to install and run spice locally. The builder defaults to the local runtime:

```csharp
using Spice;

using var client = new SpiceClientBuilder()
    .Build();

var data = await client.SqlAsync("SELECT trip_distance, total_amount FROM taxi_trips ORDER BY trip_distance DESC LIMIT 10;");
```

Or using a custom flight address:

```csharp
using var client = new SpiceClientBuilder()
    .WithFlightAddress("http://localhost:50051")
    .Build();
```

### Default endpoints

| Target         | Arrow Flight                     | HTTP                      |
| -------------- | -------------------------------- | ------------------------- |
| Spice.ai Cloud | `https://flight.spiceai.io:443`  | `https://data.spiceai.io` |
| Local runtime  | `http://localhost:50051`         | `http://localhost:8090`   |

The Cloud endpoints can be set with `SPICE_FLIGHT_URL` and `SPICE_HTTP_URL`, and the local endpoints with `SPICE_LOCAL_FLIGHT_URL` and `SPICE_LOCAL_HTTP_URL`.

### Refreshing a dataset

`RefreshDatasetAsync(string datasetName)` triggers a refresh of an accelerated dataset.

### Contributing

Contribute to or file an issue with the `spice-dotnet` library at: [https://github.com/spiceai/spice-dotnet](https://github.com/spiceai/spice-dotnet)
