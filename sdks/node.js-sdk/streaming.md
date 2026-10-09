# Streaming

The `@spiceai/spice` SDK supports streaming partial results as they become available.

This can be used to enable more efficient pipelining scenarios where processing each row of the result set can happen independently.

The [`SpiceClient.sql`](api-reference.md#spiceclient-methods) method takes an optional `onData` callback that will be passed partial results as they become available.

```typescript
public async sql(
    queryText: string,
    optionsOrCallback?: SqlQueryOptions | ((data: Table) => void),
    onData?: (data: Table) => void,
    headers?: { [key: string]: string }
  ): Promise<Table>
```

A callback may be passed either as the second argument or as `onData`.

In this example, we retrieve all 10,000 suppliers from the TPCH Suppliers table. This query retrieves all suppliers in a single call:

```javascript
import { SpiceClient } from "@spiceai/spice";

const spiceClient = new SpiceClient({ apiKey: process.env.API_KEY! });
const query = `
SELECT s_suppkey, s_name
FROM tpch.supplier
`;
const allSuppliers = await spiceClient.sql(query);
allSuppliers.toArray().forEach((row) => {
    processSupplier(row);
});
```

This call will wait for the promise returned by `sql()` to complete, returning all 10,000 suppliers.

Alternatively, data can be processed as it is streamed to the SDK. Provide a callback function to the `onData` parameter, which will be called with every partial set of data streamed to the SDK:

```javascript
import { SpiceClient } from "@spiceai/spice";

const spiceClient = new SpiceClient({ apiKey: process.env.API_KEY! });
const query = `
SELECT s_suppkey, s_name
FROM tpch.supplier
`;
await spiceClient.sql(query, undefined, (partialData) => {
    partialData.toArray().forEach((row) => {
        processSupplier(row);
    });
});
```

{% hint style="info" %}
On the Arrow Flight transport the first message carries the schema and is not passed to `onData`; each subsequent message is delivered as its own `Table`. The promise still resolves with the complete result assembled from every chunk.
{% endhint %}
