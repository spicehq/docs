# API Reference

## SpiceClient(params)

The top-level object that connects to Spice.ai.

* `params.apiKey` (string, optional): API key to authenticate with the endpoint.
* `params.httpUrl` (string, optional): URL of the HTTP endpoint, including the scheme (default: `http://127.0.0.1:8090`).
* `params.flightUrl` (string, optional): Host and port of the Flight endpoint, without a scheme (default: `127.0.0.1:50051`, using local Spice Runtime).
* `params.flightTlsEnabled` (boolean, optional): Use TLS for Flight. Defaults to `false` for a localhost address and `true` otherwise.
* `params.userAgent` (string, optional): Prepended to the reported user agent.
* `params.customHeaders` (object, optional): Additional headers to send with each request.
* `params.flightOnly` (boolean, optional): Use only the Flight transport (default: `false`).
* `params.httpOnly` (boolean, optional): Use only the HTTP transport (default: `false`). Setting both `flightOnly` and `httpOnly` throws.
* `params.logging` (boolean, optional): Enable or disable logging output (default: `true`).

Supplying only an `apiKey` — with neither URL set — selects the Spice.ai Cloud endpoints for both.

Default connection to local Spice Runtime:

```javascript
import { SpiceClient } from "@spiceai/spice";

const spiceClient = new SpiceClient();
```

Connect to Spice.ai Cloud Platform:

```javascript
import { SpiceClient } from "@spiceai/spice";

const spiceClient = new SpiceClient({
    apiKey: 'API_KEY',
    httpUrl: 'https://data.spiceai.io',
    flightUrl: 'flight.spiceai.io:443'
});
```

Or using shorthand:

```javascript
import { SpiceClient } from "@spiceai/spice";

const spiceClient = new SpiceClient('API_KEY');
```

### SpiceClient Methods

#### `sql(query, options?, onData?, headers?)` — Execute SQL queries

The recommended method for executing SQL queries. Returns an Apache Arrow [Table](https://arrow.apache.org/docs/js/classes/Arrow_dom.Table.html).

* `query` (string, required): The SQL query to execute.
* `options` (object, optional): Query options including `parameters` for parameterized queries. A callback may be passed in this position instead of `onData`.
* `onData` (callback, optional): Callback for handling [streaming](streaming.md) data.
* `headers` (object, optional): Custom headers to include with the request.

```javascript
// Standard query
const table = await spiceClient.sql("SELECT * FROM tpch.lineitem LIMIT 10");
table.toArray().forEach((row) => {
  console.log(JSON.stringify(row));
});

// Parameterized query
const table = await spiceClient.sql(
  'SELECT * FROM taxi_trips WHERE passenger_count = $1 AND trip_distance > $2 LIMIT 10',
  { parameters: [2, 5.0] }
);
```

{% hint style="info" %}
Rows returned by `toArray()` are plain objects when the result contains a decimal, timestamp, list, or struct column, and Arrow row proxies otherwise. Use plain property access or `JSON.stringify(row)` rather than calling `row.toJSON()`, which is not present on the plain-object form.
{% endhint %}

Get all elements for a column by calling [`getChild(name: string)`](https://arrow.apache.org/docs/js/classes/Arrow_dom.Table.html#getChild):

```javascript
const table = await spiceClient.sql(
  'SELECT sum(l_extendedprice) as sum_extendedprice FROM tpch.lineitem'
);
let sumExtendedPrice = table.getChild("sum_extendedprice");
console.log(sumExtendedPrice?.toJSON());
```

#### `sqlJson(query, headers?)` — Execute SQL queries with JSON results

Returns results in JSON format with schema information.

* `query` (string, required): The SQL query to execute.
* `headers` (object, optional): Custom headers to include with the request.

```javascript
const result = await spiceClient.sqlJson('SELECT name, age FROM users LIMIT 5');

console.log(`Returned ${result.row_count} rows`);
console.log('Schema:', result.schema);
console.log('Data:', result.data);
console.log(`Query took ${result.execution_time_ms}ms`);
```

The response includes:

* `row_count`: Number of rows returned
* `schema`: Schema information with field names and types
* `data`: Array of row objects
* `execution_time_ms`: Query execution time in milliseconds

#### `nsql(query, options?)` — Natural language to SQL

Converts natural language queries into SQL and executes them.

* `query` (string, required): The natural language query.
* `options` (object, optional):
  * `datasets` (array, optional): Dataset names to sample from when building the model context. A sampling hint only — it does not restrict which tables the query can target.
  * `model` (string, optional): Model to use for SQL generation. When omitted, the single compatible model configured in the app is used; if none or more than one is configured, the request fails.
  * `sample_data_enabled` (boolean, optional): Include sample data in context (default: `false`).

See the [Text-to-SQL API](../../cloud/api/nsql.md) for the full contract.

```javascript
const result = await spiceClient.nsql('Show me the top 5 customers by total sales');

console.log('Generated SQL:', result.sql);
console.log('Results:', result.data);
console.log(`Returned ${result.row_count} rows`);
```

#### `refreshAcceleration(dataset, options?)` — Trigger dataset refresh

Triggers an on-demand refresh for an accelerated dataset.

* `dataset` (string, required): Name of the dataset to refresh.
* `options` (object, optional):
  * `refresh_mode` (string): `'full'`, `'append'`, `'changes'`, or `'disabled'`.
  * `refresh_sql` (string): Custom SQL query for the refresh.
  * `refresh_jitter_max` (string): Maximum jitter time for refresh scheduling.

```javascript
await spiceClient.refreshAcceleration('my_dataset');

// With options
await spiceClient.refreshAcceleration('my_dataset', {
  refresh_mode: 'full',
  refresh_sql: 'SELECT * FROM source WHERE updated_at > NOW() - INTERVAL 1 DAY',
});
```

#### `isSpiceHealthy()` — Check runtime health

Checks if the Spice runtime is healthy. This endpoint is **unauthenticated**.

```javascript
const isHealthy = await spiceClient.isSpiceHealthy();
```

#### `isSpiceReady()` — Check runtime readiness

Checks if the Spice runtime is ready to accept queries. This endpoint is **authenticated** if an API key is configured.

```javascript
const isReady = await spiceClient.isSpiceReady();
```

#### `search(query, options?)` — Search datasets

Runs a search query against the app's datasets.

```javascript
const results = await spiceClient.search('quarterly revenue trends');
```

#### `query(sql, onData?, headers?)` — Deprecated query method

Deprecated and scheduled for removal in a future version. Use `sql()` instead.

```javascript
const table = await spiceClient.query("SELECT * FROM tpch.lineitem LIMIT 10");
```

#### `setMaxRetries(retries)` — Configure connection retries

Configures the maximum number of connection retry attempts (default: 3).

```javascript
spiceClient.setMaxRetries(5); // Setting to 0 disables retries
```
