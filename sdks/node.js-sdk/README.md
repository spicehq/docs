---
icon: node
---

# Node.js SDK

The Node.js SDK [spice.js](https://www.npmjs.com/package/@spiceai/spice) is the easiest way to use and query [Spice.ai](https://spice.ai) with Node.js.

It uses [Apache Arrow Flight](https://arrow.apache.org/docs/format/Flight.html) to efficiently stream data to the client and [Apache Arrow](https://arrow.apache.org/) Records as data frames which are then easily converted to JavaScript objects/arrays or JSON.

### Requirements

* [Node.js 20+](https://nodejs.org/)

### Installation

{% tabs %}
{% tab title="npm" %}
```sh
npm install @spiceai/spice@latest --save
```
{% endtab %}

{% tab title="yarn" %}
```sh
yarn add @spiceai/spice
```
{% endtab %}
{% endtabs %}

### Usage

Import `SpiceClient` and instantiate a new instance with your configuration.

You can then submit queries using the `sql` method.

```javascript
import { SpiceClient } from "@spiceai/spice";

const spiceClient = new SpiceClient({
  apiKey: 'API_KEY',
  httpUrl: 'https://data.spiceai.io',
  flightUrl: 'flight.spiceai.io:443',
});
const table = await spiceClient.sql('SHOW TABLES;');
console.table(table.toArray());
```

Or using the shorthand with just an API key:

```javascript
const spiceClient = new SpiceClient('API_KEY');
```

`SpiceClient` accepts a config object or a string API key:

* `apiKey` (string, optional): API key to authenticate with the endpoint.
* `flightUrl` (string, optional): Host and port of the Flight endpoint, without a scheme (default: `127.0.0.1:50051`).
* `httpUrl` (string, optional): URL of the HTTP endpoint, including the scheme (default: `http://127.0.0.1:8090`).
* `flightTlsEnabled` (boolean, optional): Use TLS for Flight. Defaults to `false` for a localhost address and `true` otherwise.
* `userAgent` (string, optional): Prepended to the reported user agent.
* `customHeaders` (object, optional): Additional headers to send with each request.
* `flightOnly` (boolean, optional): Use only the Flight transport (default: `false`).
* `httpOnly` (boolean, optional): Use only the HTTP transport (default: `false`). Setting both `flightOnly` and `httpOnly` throws.
* `logging` (boolean, optional): Enable or disable logging output (default: `true`).

{% hint style="info" %}
Supplying only an `apiKey` — with neither `httpUrl` nor `flightUrl` — selects the Spice.ai Cloud endpoints (`https://data.spiceai.io` and `flight.spiceai.io:443`). Setting either URL explicitly keeps the local defaults for the other.
{% endhint %}

#### **`sqlJson(query: string)` - Execute SQL queries with JSON results**

The `sqlJson()` method executes SQL queries and returns results in a JSON format with schema information.

```js
const result = await spiceClient.sqlJson('SELECT name, age FROM users LIMIT 5');

console.log(`Returned ${result.row_count} rows`);
console.log('Schema:', result.schema);
console.log('Data:', result.data);
console.log(`Query took ${result.execution_time_ms}ms`);

// Access individual rows
result.data.forEach((row) => {
  console.log(`${row.name} is ${row.age} years old`);
});
```

The response includes:

* `row_count`: Number of rows returned
* `schema`: Schema information with field names and types
* `data`: Array of row objects
* `execution_time_ms`: Query execution time in milliseconds

#### Other methods

* `nsql(query, options?)` — generate and run SQL from a natural language question. See the [Text-to-SQL API](../../cloud/api/nsql.md).
* `search(query, options?)` — run a search query against the app's datasets.
* `refreshAcceleration(dataset, options?)` — trigger a refresh of an accelerated dataset.
* `isSpiceReady()` and `isSpiceHealthy()` — check the runtime's readiness and health.

See the [API reference](api-reference.md) for full signatures.

### Usage with local Spice runtime

Follow the [quickstart guide](https://github.com/spiceai/spiceai?tab=readme-ov-file#%EF%B8%8F-quickstart-local-machine) to install and run spice locally.

```javascript
import { SpiceClient } from '@spiceai/spice';

const main = async () => {
  // uses connection to local runtime by default
  const spiceClient = new SpiceClient();

  // or use custom connection params:
  // const spiceClient = new SpiceClient({
  //   httpUrl: 'http://my_spice_http_host',
  //   flightUrl: 'my_spice_flight_host',
  // });

  const table = await spiceClient.sql(
    'SELECT trip_distance, total_amount FROM taxi_trips ORDER BY trip_distance DESC LIMIT 10;'
  );
  console.table(table.toArray());
};

main();
```

Check [Spice OSS documentation](https://docs.spiceai.org/clients) to learn more.

### Connection retry

From [version 1.0.1](https://github.com/spiceai/spice.js/releases/tag/v1.0.1) the `SpiceClient` implements connection retry mechanism (3 attempts by default). The number of attempts can be configured via `setMaxRetries`:

```
const spiceClient = new SpiceClient('API_KEY');
spiceClient.setMaxRetries(5); // Setting to 0 will disable retries
```

Retries are performed for connection and system internal errors. It is the SDK user's responsibility to properly handle other errors, for example `RESOURCE_EXHAUSTED (HTTP 429)`.

### Contributing

Contribute to or file an issue with the `spice.js` library at: [https://github.com/spiceai/spice.js](https://github.com/spiceai/spice.js).
