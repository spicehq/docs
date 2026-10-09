---
description: Rust SDK for Spice.ai
icon: rust
---

# Rust SDK

The Rust SDK crate [`spiceai`](https://crates.io/crates/spiceai) is the easiest way to query [Spice.ai](https://spice.ai) from Rust. It is developed in the [spice-rs](https://github.com/spiceai/spice-rs) repository.

It uses [Apache Arrow Flight](https://arrow.apache.org/docs/format/Flight.html) to efficiently stream data to the client and [Apache Arrow](https://arrow.apache.org/) Records as data frames.

### Requirements

* Rust 1.93.1 or later — the crate is edition 2024
* [Tokio](https://tokio.rs/) — every client method is `async`

### Installation

Add Spice SDK

```bash
cargo add spiceai
```

Note the crate is named `spiceai`, while the repository is named `spice-rs`. All imports use `spiceai`.

### Usage

1\. Create a client by providing your API key to `ClientBuilder`. Get your free API key at [spice.ai](https://spice.ai/).

```rust
use spiceai::ClientBuilder;

#[tokio::main]
async fn main() {
  let client = ClientBuilder::new()
    .api_key("API_KEY")
    .use_spiceai_cloud()
    .build()
    .await
    .unwrap();
}
```

`ClientBuilder` also accepts `.flight_url()` to set a custom endpoint, plus `.user_agent()`, `.max_retries()` (default 3), and `.cache_control()`. `.flight_url()` and `.use_spiceai_cloud()` are last-writer-wins, in either order.

For a Cloud connection with no other configuration, `spiceai::Client::new("API_KEY").await` is equivalent.

2\. Execute a query and get back a stream of Apache Arrow record batches.

```rust
let mut flight_data_stream = client.sql("SELECT * FROM tpch.lineitem LIMIT 10;").await.expect("Error executing query");
```

The binding must be `mut`, because advancing the stream takes a mutable borrow.

3\. Iterate through the stream to access the records. `StreamExt` must be in scope; the crate re-exports it.

```rust
use spiceai::StreamExt;

while let Some(batch) = flight_data_stream.next().await {
    match batch {
        Ok(batch) => {
            /* process batch */
            println!("{:?}", batch)
        },
        Err(e) => {
            /* handle error */
        },
    };
}
```

### Parameterized queries

`sql_with_params` binds parameters supplied as an Arrow `RecordBatch` whose fields are named `$1`, `$2`, and so on. For a single row of scalar values, `sql_with_bindings` builds that batch from a `QueryParameters` list:

```rust
use spiceai::QueryParameters;

let mut stream = client
    .sql_with_bindings(
        "SELECT * FROM taxi_trips WHERE VendorID = $1 AND fare_amount > $2 LIMIT 10;",
        QueryParameters::new().push(1_i32).push(1.0_f64),
    )
    .await
    .expect("Error executing query");
```

### Asynchronous queries

`query` and `query_with_bindings` submit a query for asynchronous execution and return a `QueryJob` handle instead of a stream. They require the runtime to be running in distributed (scheduler) mode.

These methods go over HTTP rather than Flight, so the client needs `.http_url()` — without it, `query` returns `QueryError::HttpError` before submitting anything. `results()` does not block: it returns `QueryError::NotReady` while the job is still pending or running, so wait on the handle first with `wait()` or `wait_timeout()`.

```rust
let client = ClientBuilder::new()
  .http_url("http://localhost:8090")
  .build()
  .await
  .unwrap();

let job = client.query("SELECT * FROM taxi_trips").await.expect("Error submitting query");
job.wait().await.expect("Error waiting for the query");
let batches = job.results().await.expect("Error reading results");
```

### Usage with local Spice runtime

Follow the [quickstart guide](https://github.com/spiceai/spiceai?tab=readme-ov-file#%EF%B8%8F-quickstart-local-machine) to install and run spice locally. `ClientBuilder` defaults to the local runtime, so no endpoint is needed:

```rust
use spiceai::ClientBuilder;

#[tokio::main]
async fn main() {
  let client = ClientBuilder::new()
    .build()
    .await
    .unwrap();

  let data = client.sql("SELECT trip_distance, total_amount FROM taxi_trips ORDER BY trip_distance DESC LIMIT 10;").await;
}
```

### Default endpoints

| Target         | Arrow Flight                  |
| -------------- | ----------------------------- |
| Spice.ai Cloud | `https://flight.spiceai.io`   |
| Local runtime  | `http://localhost:50051`      |

`ClientBuilder` defaults to the local runtime; `Client::new(api_key)` defaults to Spice.ai Cloud. Override either with `.flight_url()`.

### Contributing

Contribute to or file an issue with the `spiceai` crate at: [https://github.com/spiceai/spice-rs](https://github.com/spiceai/spice-rs)
