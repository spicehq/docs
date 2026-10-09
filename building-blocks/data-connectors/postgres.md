---
description: 'PostgreSQL Data Connector Documentation'
---

# PostgreSQL Data Connector

PostgreSQL is an advanced open-source relational database management system known for its robustness, extensibility, and support for SQL compliance.

The PostgreSQL Server Data Connector enables federated/accelerated SQL queries on data stored in PostgreSQL databases.

```yaml
datasets:
  - from: postgres:my_table
    name: my_dataset
    params:
      pg_host: localhost
      pg_port: 5432
      pg_db: my_database
      pg_user: my_user
      pg_pass: ${secrets:my_pg_pass}
```

## Configuration

### `from`

The `from` field takes the form `postgres:my_table` where `my_table` is the table identifier in the PostgreSQL server to read from.

The fully-qualified table name (`database.schema.table`) can also be used in the `from` field.

```yaml
datasets:
  - from: postgres:my_database.my_schema.my_table
    name: my_dataset
    params: ...
```

{% hint style="info" %}
Unquoted identifiers are normalized to lowercase. To reference a table or schema with mixed-case characters, wrap each case-sensitive part in double quotes: `postgres:my_schema."MixedCaseTable"`. See [Identifier Case Sensitivity](README.md#identifier-case-sensitivity-and-quoting).
{% endhint %}

### `name`

The dataset name. This will be used as the table name within Spice.

Example:

```yaml
datasets:
  - from: postgres:my_database.my_schema.my_table
    name: cool_dataset
    params: ...
```

```sql
SELECT COUNT(*) FROM cool_dataset;
```

```shell
+----------+
| count(*) |
+----------+
| 6001215  |
+----------+
```

### `params`

The connection to PostgreSQL can be configured by providing the following `params`:

| Parameter Name         | Description                                                                                                                                                                                                                                                                                                                                 |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `pg_host`              | The hostname of the PostgreSQL server.                                                                                                                                                                                                                                                                                                      |
| `pg_port`              | The port of the PostgreSQL server.                                                                                                                                                                                                                                                                                                          |
| `pg_db`                | The name of the database to connect to.                                                                                                                                                                                                                                                                                                     |
| `pg_user`              | The username to connect with.                                                                                                                                                                                                                                                                                                               |
| `pg_pass`              | The password to connect with. Use the secret replacement syntax to load the password from a secret store, e.g. `${secrets:my_pg_pass}`.                                                                                                                                                                                                     |
| `pg_sslmode`           | Optional. Specifies the SSL/TLS behavior for the connection, supported values: `verify-full` (default) - requires SSL, valid root certificate, and matching server host name; `verify-ca` - requires TLS and valid root certificate; `require` - requires TLS; `prefer` - tries TLS but connects insecurely if not supported; `disable` - does not use TLS. |
| `pg_sslrootcert`       | Optional parameter specifying the path to a custom PEM certificate that the connector will trust.                                                                                                                                                                                                                                           |
| `connection_pool_size` | Optional. The maximum number of connections to keep open in the connection pool. Default is 10.                                                                                                                                                                                                                                             |

## Types

The table below shows the PostgreSQL data types supported, along with the type mapping to Apache Arrow types in Spice.

| PostgreSQL Type | Arrow Type                       |
| --------------- | -------------------------------- |
| `int2`          | `Int16`                          |
| `int4`          | `Int32`                          |
| `int8`          | `Int64`                          |
| `money`         | `Int64`                          |
| `float4`        | `Float32`                        |
| `float8`        | `Float64`                        |
| `numeric`       | `Decimal128`                     |
| `text`          | `Utf8`                           |
| `varchar`       | `Utf8`                           |
| `bpchar`        | `Utf8`                           |
| `uuid`          | `Utf8`                           |
| `bytea`         | `Binary`                         |
| `bool`          | `Boolean`                        |
| `json`          | `LargeUtf8`                      |
| `timestamp`     | `Timestamp(Nanosecond, None)`    |
| `timestampz`    | `Timestamp(Nanosecond, TimeZone` |
| `date`          | `Date32`                         |
| `time`          | `Time64(Nanosecond)`             |
| `interval`      | `Interval(MonthDayNano)`         |
| `point`         | `FixedSizeList(Float64[2])`      |
| `int2[]`        | `List(Int16)`                    |
| `int4[]`        | `List(Int32)`                    |
| `int8[]`        | `List(Int64)`                    |
| `float4[]`      | `List(Float32)`                  |
| `float8[]`      | `List(Float64)`                  |
| `text[]`        | `List(Utf8)`                     |
| `bool[]`        | `List(Boolean)`                  |
| `bytea[]`       | `List(Binary)`                   |
| `geometry`      | `Binary`                         |
| `geography`     | `Binary`                         |
| `enum`          | `Dictionary(Int8, Utf8)`         |
| Composite Types | `Struct`                         |

{% hint style="info" %}
The Postgres federated queries may result in unexpected result types due to the difference in DataFusion and Postgres size increase rules. Explicitly specify the expected output type of aggregation functions when writing queries involving Postgres tables in Spice. For example, rewrite `SUM(int_col)` into `CAST (SUM(int_col) as BIGINT)`.
{% endhint %}

## Examples

### Connecting using Username/Password

```yaml
datasets:
  - from: postgres:my_database.my_schema.my_table
    name: my_dataset
    params:
      pg_host: localhost
      pg_port: 5432
      pg_db: my_database
      pg_user: my_user
      pg_pass: ${secrets:my_pg_pass}
```

### Connect using SSL

```yaml
datasets:
  - from: postgres:my_database.my_schema.my_table
    name: my_dataset
    params:
      pg_host: localhost
      pg_port: 5432
      pg_db: my_database
      pg_user: my_user
      pg_pass: ${secrets:my_pg_pass}
      pg_sslmode: verify-ca
      pg_sslrootcert: ./custom_cert.pem
```

### Separate dataset/accelerator secrets

Specify different secrets for a PostgreSQL source and acceleration:

```yaml
datasets:
  - from: postgres:my_schema.my_table
    name: my_dataset
    params:
      pg_host: localhost
      pg_port: 5432
      pg_db: my_database
      pg_user: my_user
      pg_pass: ${secrets:pg1_pass}
    acceleration:
      engine: postgres
      params:
        pg_host: localhost
        pg_port: 5433
        pg_db: acceleration
        pg_user: two_user_two_furious
        pg_pass: ${secrets:pg2_pass}
```
