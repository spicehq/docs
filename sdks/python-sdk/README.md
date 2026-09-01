---
icon: python
---

# Python SDK

The [Python SDK](https://github.com/spiceai/spicepy) `spicepy` queries [Spice.ai](https://spice.ai) from Python. It uses [Apache Arrow Flight](https://arrow.apache.org/docs/format/Flight.html) to stream results, returning Apache Arrow records that convert directly into pandas dataframes.

### Requirements

* Python 3.11 or later

The following packages are installed automatically:

* `pyarrow`
* `pandas`
* `certifi`
* `requests`

### Installation

Install from the [GitHub repository](https://github.com/spiceai/spicepy), pinned to a release tag:

```bash
pip install git+https://github.com/spiceai/spicepy@v4.0.0
```

{% hint style="danger" %}
Do **not** run `pip install spicepy`. The `spicepy` name on PyPI belongs to an unrelated third-party project, not to this SDK. Install from the GitHub URL above.
{% endhint %}

To use [parameterized queries](#parameterized-queries), two additional packages are required:

```bash
pip install adbc-driver-flightsql adbc-driver-manager
```

### Usage

Create a `Client`, then call `sql()`:

```python
from spicepy import Client

client = Client(
    api_key='API_KEY',
    flight_url='grpc+tls://us-east-1-prod-aws-flight.spiceai.io',
)

data = client.sql('SELECT trip_distance, total_amount FROM taxi_trips ORDER BY trip_distance DESC LIMIT 10;', timeout=5*60)
pd = data.read_pandas()
```

`Client` has the following arguments, all optional:

* **api\_key** (string): Project API key, used to authenticate with Spice.ai Cloud. Falls back to the `SPICE_API_KEY` environment variable.
* **flight\_url** (string): Arrow Flight endpoint (default: `grpc://localhost:50051`). Use `grpc+tls://` for TLS and `grpc://` for plaintext.
* **http\_url** (string): HTTP endpoint, used for dataset refreshes (default: `http://localhost:8090`).

  Both default to the **local runtime**, so querying Spice.ai Cloud means passing the Cloud endpoint explicitly. The examples on this page use `us-east-1`; for a project in another region, substitute your project's CNAME — see [Default endpoints](../README.md#default-endpoints). The region-agnostic `flight.spiceai.io` and `data.spiceai.io` hostnames also still work, and resolve to the `us-east-1` endpoints.
* **tls\_root\_cert** (Path or string): Path to the TLS certificate to use for the secure connection (omit for automatic detection).
* **user\_agent** (string): Overrides the reported user agent.

{% hint style="warning" %}
The `SPICE_API_KEY` environment variable authenticates HTTP requests only — it is not applied to Arrow Flight. Pass `api_key` to the constructor when querying Spice.ai Cloud.
{% endhint %}

Once a `Client` is obtained, queries can be made using the `sql()` function, which returns a `pyarrow.flight.FlightStreamReader`. It has the following arguments:

* **query** (string, required): The SQL query.
* **timeout** (int, optional): The timeout in seconds.

If no timeout is specified, it will default to a 10 min timeout then cancel the query, and a `TimeoutError` exception will be raised.

Call `read_pandas()` to read the whole result into a dataframe, or read it incrementally — see [Streaming](streaming.md).

### Usage with local Spice runtime

Follow the [quickstart guide](https://github.com/spiceai/spiceai?tab=readme-ov-file#%EF%B8%8F-quickstart-local-machine) to install and run spice locally. `flight_url` and `http_url` both already default to the local runtime, so no arguments are required:

```python
from spicepy import Client

client = Client()
data = client.sql('SELECT trip_distance, total_amount FROM taxi_trips ORDER BY trip_distance DESC LIMIT 10;', timeout=5*60)
pd = data.read_pandas()
```

{% hint style="info" %}
Pass `api_key` (and, for Cloud, `flight_url`/`http_url` — see [Usage](#usage) above) when connecting to Spice.ai Cloud instead of a local runtime.
{% endhint %}

### Parameterized queries

`sql_with_params(sql, params)` binds positional `$1`, `$2` placeholders and returns a `pyarrow.RecordBatchReader`. It requires the two ADBC packages listed under [Installation](#installation).

```python
reader = client.sql_with_params(
    'SELECT trip_distance, fare_amount FROM taxi_trips WHERE trip_distance > $1 LIMIT 10',
    [5.0],
)

for batch in reader:
    print(batch.to_pandas())
```

Parameter values may be plain Python values, whose Arrow type is inferred, or `(value, pyarrow_type)` tuples to set the type explicitly. Pass `[]` for a query with no parameters; `None` raises `ValueError`.

### Asynchronous queries

`query(sql)` and `query_with_params(sql, params)` submit a query for asynchronous execution instead of streaming results back. Both return a `QueryJob` handle used to wait for completion and fetch results, and both require `http_url` to be configured and the runtime to be running in distributed (scheduler) mode.

```python
job = client.query('SELECT * FROM taxi_trips')
job.wait()

for row in job.results():
    print(row)
```

Parameters passed to `query_with_params` must be JSON-encodable values — the `(value, pyarrow_type)` tuple form is accepted only by `sql_with_params`.

### Refreshing a dataset

`refresh_dataset(dataset, refresh_opts=None)` triggers a refresh of an accelerated dataset over the HTTP endpoint. `RefreshOpts` accepts `refresh_sql`, `refresh_mode`, and `refresh_jitter_max`.

```python
from spicepy import Client, RefreshOpts

client.refresh_dataset('taxi_trips', RefreshOpts(refresh_mode='full'))
```

### Contributing

Contribute to or file an issue with the `spicepy` library at: [https://github.com/spiceai/spicepy](https://github.com/spiceai/spicepy)
