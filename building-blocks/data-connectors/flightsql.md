---
description: 'Flight SQL Data Connector Documentation'
---

# FlightSQL Data Connector

Connect to any Flight SQL compatible server (e.g. Influx 3.0, CnosDB, other Spice runtimes!) as a connector for federated SQL queries.

```yaml
- from: flightsql:my_catalog.good_schemas.cool_dataset
  name: cool_dataset
  params:
    flightsql_endpoint: http://127.0.0.1:50051
    flightsql_username: spicy
    flightsql_password: ${secrets:my_flightsql_pass}
```

## Configuration

### `from`

The `from` field takes the form `flightsql:dataset` where `dataset` is the fully qualified name of the dataset to read from.

{% hint style="info" %}
Unquoted identifiers are normalized to lowercase. To reference a dataset with mixed-case characters, wrap each case-sensitive part in double quotes: `flightsql:my_catalog."MySchema"."MyTable"`. See [Identifier Case Sensitivity](README.md#identifier-case-sensitivity-and-quoting).
{% endhint %}

### `name`

The dataset name. This will be used as the table name within Spice.

### `params`

| Parameter name       | Description                                                                                                                                                                                                                                                  |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `flightsql_endpoint` | The Apache Flight endpoint used to connect to the Flight SQL server.                                                                                                                                                                                         |
| `flightsql_username` | Optional. The username to use in the underlying Apache flight Handshake Request to authenticate to the server (see [reference](https://arrow.apache.org/docs/format/Flight.html#authentication)).                                                            |
| `flightsql_password` | Optional. The password to use in the underlying Apache flight Handshake Request to authenticate to the server. Use the [secret replacement syntax](../secret-stores/index.md) to load the password from a secret store, e.g. `${secrets:my_flightsql_pass}`. |
