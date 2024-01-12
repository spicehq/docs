---
description: Rust SDK for Spice.ai
---

# 🆕 Rust SDK

The [Rust SDK](https://github.com/spiceai/spice-rs) `spice-rs` is the easiest way to query [Spice.ai](https://spice.ai) from Rust.

It uses [Apache Arrow Flight](https://arrow.apache.org/docs/format/Flight.html) to efficiently stream data to the client and [Apache Arrow](https://arrow.apache.org/) Records as data frames.

### Requirements

* [Rust 1.73.0](https://blog.rust-lang.org/2023/10/05/Rust-1.73.0.html)

### Installation

Add the following to your `Cargo.toml`:

```rust
[dependencies]
spice-rs = { git = "https://github.com/spiceai/spice-rs", tag = "v1.0.2" }
```

### Usage

1\. Create a `SpiceClient` by passing in your API key. Get your free API key at [spice.ai](https://spice.ai/).

```rust
use spice_rs::Client;

let mut client = Client::new("API_KEY").await;
```

2\. Execute a query and get back an Apache Arrow [Flight Record Batch Stream](https://arrow.apache.org/rust/arrow\_flight/decode/struct.FlightRecordBatchStream.html).

```rust
let mut flight_data_stream = client.query("SELECT * FROM eth.recent_blocks LIMIT 10;").await.expect("Error executing query");
```

3\. Iterate through the reader to access the records.

```rust
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
{% hint style="info" %}
Add [futures](https://docs.rs/futures/) crate and `use futures::StreamExt;` if you see `no method named next found for struct` compile error for `flight_data_stream.next()`.
{% endhint %}

