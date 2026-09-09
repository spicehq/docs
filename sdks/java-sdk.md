---
icon: java
---

# Java SDK

The [`Java SDK`](https://github.com/spiceai/spice-java) is the easiest way to query the Spice Cloud Platform from Java.

It uses [Apache Arrow Flight](https://arrow.apache.org/docs/format/Flight.html) to efficiently stream data to the client and [Apache Arrow](https://arrow.apache.org/) Records as data frames.

### Supported Java Versions

The library targets **Java 11** and above, and is tested against the following implementations:

* Microsoft OpenJDK 11, 17, 21
* Eclipse Temurin 21, 23, 24
* Oracle JDK 17, 21, 23, 24, 25

### Installation

{% tabs %}
{% tab title="Maven" %}
```xml
<dependency>
    <groupId>ai.spice</groupId>
    <artifactId>spiceai</artifactId>
    <version>0.8.0</version>
    <scope>compile</scope>
</dependency>
```
{% endtab %}

{% tab title="Gradle" %}
```groovy
implementation 'ai.spice:spiceai:0.8.0'
```
{% endtab %}
{% endtabs %}

### Usage

1\. Import the package.

```java
import ai.spice.SpiceClient;
```

2\. Create a `SpiceClient` by providing your API key. Get your free API key at [spice.ai](https://spice.ai/).

`SpiceClient` implements `AutoCloseable`, so use it in a try-with-resources block.

```java
try (SpiceClient spice = SpiceClient.builder()
        .withApiKey(ApiKey)
        .withSpiceCloud()
        .build()) {
    // query here
}
```

The builder also accepts `withFlightAddress(URI)`, `withHttpAddress(URI)`, `withUserAgent(String)`, `withMaxRetries(int)`, and `withArrowMemoryLimitMB(long)`. The API key must be in `appId|key` form.

For mutual TLS, connection pooling, and query deadlines, see [Mutual TLS](#mutual-tls) and [Performance tuning](#performance-tuning).

3\. Execute a query and get back a [`FlightStream`](https://arrow.apache.org/docs/java/reference/org.apache.arrow.flight.core/org/apache/arrow/flight/FlightStream.html).

```java
FlightStream stream = spice.sql("SELECT * FROM tpch.lineitem LIMIT 10");
```

4\. Iterate through the `FlightStream` to access the records.

```java
while (stream.next()) {
    try (VectorSchemaRoot batches = stream.getRoot()) {
        System.out.println(batches.contentToTSVString());
    }
}
```

Check [full example](https://github.com/spiceai/spice-java/blob/trunk/src/main/java/ai/spice/example/ExampleSpiceCloudPlatform.java) to learn more.

### Parameterized queries

`sqlWithParams(String sql, Object... params)` binds positional `$1`, `$2` placeholders and returns an `ArrowReader`, which the caller closes.

```java
try (ArrowReader reader = spice.sqlWithParams(
        "SELECT * FROM tpch.lineitem WHERE l_quantity > $1 LIMIT 10", 10)) {
    while (reader.loadNextBatch()) {
        System.out.println(reader.getVectorSchemaRoot().contentToTSVString());
    }
}
```

Parameterized queries run on Apache Arrow Flight SQL prepared statements over the same connections as `sql()`, so they inherit the client's TLS, retry, and keep-alive settings.

Prepared statements are cached and reused, which removes the prepare round trips from every repeated query. The cache holds 64 statements by default; `withPreparedStatementCacheSize(0)` disables caching:

```java
SpiceClient spice = SpiceClient.builder()
    .withPreparedStatementCacheSize(128)
    .build();
```

Failures surface as a `FlightRuntimeException` wrapped in an `ExecutionException`.

### Asynchronous queries

`query(String sql)` and `queryWithParams(String sql, Object... params)` submit a query for asynchronous execution and return an `AsyncQuery` handle instead of streaming results. They require the runtime to be running in distributed (scheduler) mode.

```java
AsyncQuery asyncQuery = spice.query("SELECT * FROM taxi_trips");

try (ArrowReader reader = asyncQuery.results()) { // blocks until the query completes
    while (reader.loadNextBatch()) {
        System.out.println(reader.getVectorSchemaRoot().contentToTSVString());
    }
}
```

The handle also exposes `getQueryId()`, `status()` for a single poll, `waitForCompletion(Duration)` to bound the wait, and `cancel()`.

### Usage with local Spice.ai OSS runtime

Follow the [quickstart guide](https://github.com/spiceai/spiceai?tab=readme-ov-file#%EF%B8%8F-quickstart-local-machine) to install and run spice locally. The builder defaults to the local runtime:

```java
try (SpiceClient spice = SpiceClient.builder()
        .build()) {
    // query here
}
```

Or using custom flight address:

```java
try (SpiceClient spice = SpiceClient.builder()
        .withFlightAddress(new URI("grpc://my_remote_spice_instance:50051"))
        .build()) {
    // query here
}
```

{% hint style="warning" %}
Always include an explicit port. An `http://` or `https://` address is rewritten to a `grpc+tcp://` or `grpc+tls://` address using the URI's port, so an address without one resolves to an invalid port.
{% endhint %}

Check [Spice OSS documentation](https://docs.spiceai.org/sdks/java) or [Java SDK Sample](https://github.com/spiceai/samples/tree/trunk/client-sdk/spice-java-sdk-sample) to learn more

### Default endpoints

| Target         | Arrow Flight                     | HTTP                      |
| -------------- | -------------------------------- | ------------------------- |
| Spice.ai Cloud | `https://flight.spiceai.io:443`  | `https://data.spiceai.io` |
| Local runtime  | `http://localhost:50051`         | `http://localhost:8090`   |

These can also be set with the `SPICE_FLIGHT_URL` and `SPICE_HTTP_URL` environment variables.

### Mutual TLS

Connect to a Spice deployment that requires mutual TLS, or one presenting a certificate signed by a private certificate authority. All three options take a path to a PEM file:

```java
try (SpiceClient spice = SpiceClient.builder()
        .withFlightAddress(new URI("grpc+tls://spice.example-org.com:50051"))
        .withTlsClientCertFile("/etc/spice/client.crt")
        .withTlsClientKeyFile("/etc/spice/client.key")
        .withTlsRootCertFile("/etc/spice/ca.crt")
        .build()) {
    // query here
}
```

| Option                     | Purpose                                            |
| -------------------------- | -------------------------------------------------- |
| `withTlsClientCertFile`    | Client certificate presented to the server         |
| `withTlsClientKeyFile`     | Private key for the client certificate             |
| `withTlsRootCertFile`      | Certificate authority used to verify the server    |

The settings apply to Flight queries, parameterized queries, and HTTP operations. `withTlsRootCertFile` can be used on its own to trust a private CA without presenting a client certificate.

### Health and runtime status

Three methods report runtime state. None throws when the runtime is unreachable, so they are safe to poll from a health probe.

```java
boolean healthy = spice.isHealthy(); // runtime is responding
boolean ready = spice.isReady();     // datasets are loaded and queryable

for (ConnectionDetails details : spice.runtimeStatus()) {
    System.out.printf("%s (%s): %s%n",
        details.getName(), details.getEndpoint(), details.getStatus());
}
```

`isHealthy()` suits a liveness check, and `isReady()` a readiness check — a runtime can respond before its datasets finish loading.

`runtimeStatus()` returns per-connection detail and throws `ExecutionException` on failure. Each `ConnectionDetails` exposes `getName()`, `getEndpoint()`, `getStatus()`, `getRawStatus()`, and `isReady()`. `getStatus()` returns a `ComponentStatus`: `INITIALIZING`, `READY`, `DISABLED`, `ERROR`, `REFRESHING`, `SHUTTING_DOWN`, `NOT_LOADED`, or `UNKNOWN`.

{% hint style="info" %}
`UNKNOWN` means the runtime reported a status this version of the SDK does not recognize. Use `getRawStatus()` to read the value the runtime sent.
{% endhint %}

### Performance tuning

```java
SpiceClient spice = SpiceClient.builder()
    .withChannelCount(4)
    .withQueryTimeout(Duration.ofSeconds(30))
    .withPreparedStatementCacheSize(128)
    .build();
```

| Option                              | Default | Description                                                                   |
| ----------------------------------- | ------- | ----------------------------------------------------------------------------- |
| `withChannelCount(int)`             | `1`     | Size of the round-robin gRPC connection pool, for highly concurrent workloads  |
| `withQueryTimeout(Duration)`        | none    | Deadline for query planning, prepare, and bind calls; must be positive         |
| `withPreparedStatementCacheSize(int)` | `64`  | Prepared statements retained for reuse; `0` disables caching                    |

`withQueryTimeout` bounds the planning and binding calls, not the streaming of results — a long-running result stream is not cut short by it.

### Connection pooling with HikariCP

Spice can also be reached over JDBC using the Apache Arrow Flight SQL JDBC driver, which allows pooling connections with HikariCP. See the [spice-java README](https://github.com/spiceai/spice-java#readme) for a worked example.

{% hint style="warning" %}
On JDK 25 and later, start the JVM with `--sun-misc-unsafe-memory-access=allow` when using the JDBC driver. The Netty allocator bundled in the driver turns its own `sun.misc.Unsafe` path off by default from that release onward — [JEP 498](https://openjdk.org/jeps/498) only made JDK 24 *warn* on these calls, and Netty waits for the working memory-segment APIs in 25 before disabling. The driver then fails at class initialization, before the first query:

```text
java.lang.ExceptionInInitializerError
Caused by: java.lang.UnsupportedOperationException
        at org.apache.arrow.driver.jdbc.shaded.io.netty.buffer.EmptyByteBuf.memoryAddress
```

The option was added in JDK 23 and earlier releases refuse to start with it, so apply it conditionally — a Maven profile activated on `<jdk>[25,)</jdk>`, or a Gradle check on `JavaVersion.current()`. (`[23,)` also works: 23 and 24 accept the option and are unaffected by it.) `SpiceClient` itself needs no such flag.
{% endhint %}

### Refreshing a dataset

`refreshDataset(String dataset)` triggers a refresh of an accelerated dataset, optionally taking a `RefreshOptions`.

### Connection retry

The `SpiceClient` implements connection retry mechanism (3 attempts by default). The number of attempts can be configured with `withMaxRetries`:

```java
SpiceClient client = SpiceClient.builder()
    .withMaxRetries(5) // Setting to 0 will disable retries
    .build();
```

Retries are performed for connection and system internal errors. It is the SDK user's responsibility to properly handle other errors, for example `RESOURCE_EXHAUSTED (HTTP 429)`.

Attempts use exponential backoff with random jitter, capped at 10 seconds per wait. The jitter keeps a fleet of clients from retrying in lockstep, and the backoff lets the default three attempts span a load-balancer failover or a runtime restart.

{% hint style="info" %}
Because retries now wait between attempts, a query against an unreachable runtime takes longer to report failure. Use `withMaxRetries(0)` where a fast failure matters more than surviving a restart.
{% endhint %}

### Re-authentication

An expired handshake token is detected and renewed automatically: the client re-handshakes once and retries the request, so long-lived `SpiceClient` instances keep working without calling `reset()`.

`reset()` retires existing connections gracefully — in-flight queries and open result streams run to completion while new queries use fresh connections.

### Contributing

Contribute to or file an issue with the `spice-java` library at: [https://github.com/spiceai/spice-java](https://github.com/spiceai/spice-java)
