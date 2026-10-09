---
description: Golang SDK for Spice.ai
icon: golang
---

# Go SDK

The [Go SDK](https://github.com/spiceai/gospice) `gospice` is the easiest way to query [Spice.ai](https://spice.ai) from Go.

It uses [Apache Arrow Flight](https://arrow.apache.org/docs/format/Flight.html) to efficiently stream data to the client and [Apache Arrow](https://arrow.apache.org/) Records as data frames.

GoDocs are available at [pkg.go.dev/github.com/spiceai/gospice/v9](https://pkg.go.dev/github.com/spiceai/gospice/v9).

### Requirements

* [Go 1.25](https://go.dev/doc/go1.25) (or later)

### Installation

Get the **gospice** package.

```bash
go get github.com/spiceai/gospice/v9@latest
```

{% hint style="warning" %}
The module path carries the major version. Requesting `gospice/v8` resolves an older major release, not the latest.
{% endhint %}

### Usage

1\. Import the package.

```go
import gospice "github.com/spiceai/gospice/v9"
```

2\. Create a `SpiceClient` by providing your API key. Get your free API key at [spice.ai](https://spice.ai/).

```go
spice := gospice.NewSpiceClient()
defer func() { _ = spice.Close() }()
```

`Close()` releases the Flight and HTTP connections and must be called.

3\. Initialize the `SpiceClient`.

```go
if err := spice.Init(
    gospice.WithApiKey(ApiKey),
    gospice.WithSpiceCloudAddress(),
); err != nil {
    panic(fmt.Errorf("error initializing SpiceClient: %w", err))
}
```

The options passed to `Init` are package-level functions on `gospice`, not methods on the client:

| Option                     | Description                                                       |
| -------------------------- | ----------------------------------------------------------------- |
| `WithApiKey(key)`          | Project API key, in `appId\|secret` form.                             |
| `WithSpiceCloudAddress()`  | Connect to Spice.ai Cloud.                                        |
| `WithFlightAddress(addr)`  | Arrow Flight address. A `grpc://` prefix selects plaintext; otherwise TLS is used. |
| `WithHttpAddress(addr)`    | HTTP address, used for health checks and dataset refreshes.        |
| `WithUserAgent(ua)`        | Prepends to the reported user agent.                              |

4\. Execute a query and get back an [Apache Arrow Record Reader](https://pkg.go.dev/github.com/apache/arrow-go/v18/arrow/array#RecordReader).

```go
reader, err := spice.Sql(context.Background(), "SELECT * FROM tpch.lineitem LIMIT 10")
if err != nil {
    panic(fmt.Errorf("error querying: %w", err))
}
defer reader.Release()
```

5\. Iterate through the reader to access the records.

```go
for reader.Next() {
    record := reader.RecordBatch()
    defer record.Release()
    fmt.Println(record)
}
```

### Parameterized queries

`SqlWithParams` binds positional `$1`, `$2` placeholders:

```go
reader, err := spice.SqlWithParams(
    context.Background(),
    "SELECT * FROM tpch.lineitem WHERE l_quantity > $1 LIMIT 10",
    10,
)
```

### Asynchronous queries

`Query` and `QueryWithParams` submit a query for asynchronous execution and return an `*AsyncQuery` handle instead of a record reader. They require the runtime to be running in distributed (scheduler) mode.

```go
query, err := spice.Query(context.Background(), "SELECT * FROM taxi_trips")
if err != nil {
    panic(fmt.Errorf("error submitting query: %w", err))
}

reader, err := query.Results(context.Background()) // waits for completion
if err != nil {
    panic(fmt.Errorf("error reading results: %w", err))
}
defer reader.Release()
```

The handle also exposes `ID()`, `Status(ctx)`, `Wait(ctx)`, and `Cancel(ctx)`.

### Usage with local Spice runtime

Follow the [quickstart guide](https://github.com/spiceai/spiceai?tab=readme-ov-file#%EF%B8%8F-quickstart-local-machine) to install and run spice locally.

```go
spice := gospice.NewSpiceClient()
defer func() { _ = spice.Close() }()

if err := spice.Init(
    gospice.WithHttpAddress("http://127.0.0.1:8090"),
); err != nil {
    panic(fmt.Errorf("error initializing SpiceClient: %w", err))
}
```

{% hint style="info" %}
`NewSpiceClient()` defaults the Flight address to the local runtime but the HTTP address to Spice.ai Cloud. Pass `WithHttpAddress` as above so health checks and dataset refreshes reach the local runtime.
{% endhint %}

Or using a custom flight address:

```go
spice := gospice.NewSpiceClient()
defer func() { _ = spice.Close() }()

if err := spice.Init(
    gospice.WithFlightAddress("grpc://localhost:50052"),
); err != nil {
    panic(fmt.Errorf("error initializing SpiceClient: %w", err))
}
```

### Default endpoints

| Target         | Arrow Flight              | HTTP                      |
| -------------- | ------------------------- | ------------------------- |
| Spice.ai Cloud | `flight.spiceai.io:443`   | `https://data.spiceai.io` |
| Local runtime  | `grpc://localhost:50051`  | `http://localhost:8090`   |

These can also be set with the `SPICE_FLIGHT_URL`, `SPICE_HTTP_URL`, `SPICE_LOCAL_FLIGHT_URL`, and `SPICE_LOCAL_HTTP_URL` environment variables.

### Health checks

`IsSpiceHealthy(ctx)` and `IsSpiceReady(ctx)` check the HTTP endpoint's `/health` and `/v1/ready` routes and return a boolean.

### Example

Run `go run .` to execute a sample query and print the results to the console.

### Connection retry

The `SpiceClient` implements connection retry mechanism (3 attempts by default). The number of attempts can be configured via `SetMaxRetries`:

```go
spice := gospice.NewSpiceClient()
spice.SetMaxRetries(5) // Setting to 0 will disable retries
```

Retries are performed for connection and system internal errors. It is the SDK user's responsibility to properly handle other errors, for example `RESOURCE_EXHAUSTED (HTTP 429)`.

### Contributing

Contribute to or file an issue with the `gospice` library at: [https://github.com/spiceai/gospice](https://github.com/spiceai/gospice)
