---
description: User-defined scalar functions (UDFs) and table functions (UDTFs) declared in a spicepod's `functions:` section — SQL, Remote HTTP, and WebAssembly tiers.
icon: function
---

# User-Defined Functions

Spice.ai supports **user-defined functions** declared declaratively in a spicepod's `functions:` section. Each function can be a **scalar** UDF (one value per input row) or a **table** UDTF (a relation returned in a SQL `FROM` clause), and is dispatched through one of three execution tiers:

- **SQL** (`from: sql`) — in-process function whose body is a DataFusion SQL expression (scalar) or query (table).
- **Remote** (`from: http://…` or `from: https://…`) — async function that invokes a remote HTTP endpoint with a JSON request and receives a JSON response.
- **WebAssembly** (`from: wasm`) — function backed by a sandboxed WASM module invoked with Arrow IPC batches.

Functions are automatically registered into the SQL session, exposed via the `list_udfs()` UDTF and the `GET /v1/functions` HTTP endpoint, and (when scalar) surfaced to LLM tool-calling. They hot-reload when the spicepod changes on disk.

{% hint style="info" %}
**Distribution availability.** Inline SQL functions (`from: sql`) are available in all Spice distributions, including open source. **Remote HTTP** (`from: http://…`) and **WebAssembly** (`from: wasm`) tiers ship by default in Spice.ai Enterprise and Spice Cloud Platform distributions; open source users can enable them by building locally with the `http-functions` and `wasm-functions` cargo features. See [Distributions](../getting-started/distributions.md).
{% endhint %}

{% hint style="warning" %}
User-defined functions are currently in **ALPHA**. Behaviour, on-disk schema, and APIs may change without notice. A one-time warning is logged when the first `functions:` entry is registered.
{% endhint %}

{% hint style="info" %}
Set `runtime.functions.enabled: true` in `spicepod.yaml` to register user-defined functions. Without the flag, `functions:` entries log an error and are skipped at startup, and `tools:` entries with `as_sql: true` log a warning and remain LLM-only.
{% endhint %}

## Quickstart

Declare a function in `spicepod.yaml`:

```yaml
version: v2
kind: Spicepod
name: my_app

runtime:
  functions:
    enabled: true

functions:
  - name: haversine_km
    from: sql
    description: Haversine distance in kilometres.
    volatility: immutable
    signature:
      args:
        - { name: lat1, type: float64 }
        - { name: lon1, type: float64 }
        - { name: lat2, type: float64 }
        - { name: lon2, type: float64 }
      returns: float64
    body: |
      6371 * acos(
        cos(radians(lat1)) * cos(radians(lat2)) *
        cos(radians(lon2) - radians(lon1)) +
        sin(radians(lat1)) * sin(radians(lat2))
      )
```

Use it in SQL:

```sql
SELECT haversine_km(lat1, lon1, lat2, lon2) FROM trips;
```

## Schema Reference

Each entry in `functions:` is a `Function` object. Fields are strictly validated (`deny_unknown_fields` is enforced).

| Field         | Type     | Required | Description                                                                                                                                                                   |
| ------------- | -------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `name`        | string   | yes      | Identifier the function is registered under. Referenced by that name in SQL.                                                                                                  |
| `from`        | string   | yes      | Source URI selecting the execution tier. `sql`, `http://…`, `https://…`, `wasm`.                                                                                              |
| `enabled`     | bool     | no       | Defaults to `true`. Set to `false` to keep the declaration in the spicepod without registering it for SQL, tool exposure, `list_udfs()`, or `/v1/functions`.                  |
| `description` | string   | no       | Free-form description surfaced in `list_udfs()` and `GET /v1/functions`.                                                                                                      |
| `kind`        | enum     | no       | `scalar` (default) or `table`. Any other value (e.g. `aggregate`, `window`) is rejected at parse time as an unknown variant, and the spicepod fails to load.                  |
| `volatility`  | enum     | no       | `immutable`, `stable`, `volatile` (default). See [Volatility](#volatility).                                                                                                   |
| `signature`   | object   | yes      | Typed signature. See below.                                                                                                                                                   |
| `body`        | string   | tier-dep | Inline SQL expression (scalar) or `SELECT` query (table). **Required** for `from: sql` unless `body_ref` is set. **Optional** for `from: wasm` to supply a table input from SQL. **Forbidden** for `from: http*`. Mutually exclusive with `body_ref`. |
| `body_ref`    | string   | tier-dep | Path to a file whose contents are the function body. Resolved relative to the runtime's CWD. Same tier rules as `body`. Mutually exclusive with `body`.                                                                                            |
| `metadata`    | map      | no       | Free-form metadata surfaced alongside the declaration.                                                                                                                        |
| `params`      | map      | no       | Tier-specific knobs. Supports `${ secrets:KEY }` / `${ env:KEY }` interpolation. See [Remote params](#remote-params) and [WebAssembly params](#webassembly-params).            |
| `dependsOn`   | string[] | no       | Names of spicepod components that must load before this function. Inferred from SQL bodies and `params.input_table` when omitted.                                             |
| `metrics`     | object   | no       | Per-function metrics configuration.                                                                                                                                           |
| `as_tool`     | bool     | no       | Expose the function as an LLM tool. Defaults to `true` for scalar functions; table functions are always SQL-only. See [LLM Tool Exposure](#llm-tool-exposure).                |

### `signature`

```yaml
signature:
  tables:                              # optional; table inputs for the function
    - name: input
      columns:
        - { name: value, type: int64 }
  args:                                # positional scalar arguments
    - { name: x, type: int64 }
  returns: int64                       # scalar: a single Arrow type
  # returns:                           # OR — table: a list of named output columns
  #   - { name: value, type: int64 }
  #   - { name: doubled, type: int64 }
```

| Field             | Description                                                                                                                                                                                                                |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `tables`          | Optional list of declared table inputs. When present, table inputs are passed before scalar arguments at call sites. Used by [table functions](#table-functions) and scalar functions that consume a relation.             |
| `tables[].name`   | Logical input name exposed to the backend.                                                                                                                                                                                 |
| `tables[].columns`| Arrow schema declared for that input.                                                                                                                                                                                      |
| `args`            | Positional scalar argument list. Empty for niladic functions.                                                                                                                                                              |
| `args[].name`     | Argument name. Referenced by name in SQL bodies.                                                                                                                                                                           |
| `args[].type`     | Arrow logical-type string (e.g. `float64`, `utf8`, `list<int64>`, `decimal(38, 10)`, `timestamp(us, utc)`). See [Supported types](#supported-types).                                                                       |
| `returns`         | For `kind: scalar`, a single Arrow type string. For `kind: table`, a list of named output columns (each with `name` and `type`).                                                                                           |

## Execution Tiers

### SQL (`from: sql`)

The body is parsed into a DataFusion logical expression against a schema derived from the argument list, then lowered to a physical expression at build time. The standard DataFusion scalar functions (math, string, datetime), Spark built-ins, and `datafusion-functions-json` are all available in bodies.

```yaml
- name: shout
  from: sql
  volatility: immutable
  signature:
    args: [{ name: s, type: utf8 }]
    returns: utf8
  body: "upper(s)"
```

The body runs entirely in-process with no sandbox. Prefer SQL-tier UDFs for anything that can be expressed in a SQL expression — they're fastest, type-checked at startup, and don't leave the runtime.

SQL functions support the full set of Arrow logical types accepted by DataFusion, including primitives, `list<…>`, `large_list<…>`, `struct<…>`, `decimal(p, s)`, `decimal256(p, s)`, and `timestamp(unit[, timezone])`.

#### `body_ref` for longer bodies

Keep non-trivial SQL in its own file with proper editor support:

```yaml
- name: haversine_km
  from: sql
  signature: { args: [...], returns: float64 }
  body_ref: ./queries/haversine.sql
```

`body` and `body_ref` are mutually exclusive; exactly one must be set when `from: sql`.

### Remote (`from: http://…` | `https://…`)

{% hint style="info" %}
The remote tier requires the `http-functions` cargo feature. It is enabled by default in Spice.ai Enterprise and Spice Cloud Platform distributions. Open source users must build locally with `--features http-functions`; the prebuilt OSS images and install script do not ship this tier. See [Distributions](../getting-started/distributions.md).
{% endhint %}

The remote tier invokes an external HTTP endpoint, batching rows for scalar invocations and issuing a single request for table inputs. Powered by DataFusion's `AsyncScalarUDFImpl`, so the query scheduler overlaps remote calls with other work.

```yaml
- name: classify_intent
  from: http://classifier.internal/v1/classify
  volatility: volatile
  signature:
    args: [{ name: prompt, type: utf8 }]
    returns: utf8
  params:
    timeout: 2s
    batch_size: 256
    batch_concurrency: 8
    auth_bearer: ${secrets:classifier_token}
```

#### Wire contract

**Scalar functions without table arguments** issue one HTTP request per batch:

- **Request** — `POST <endpoint>`, `Content-Type: application/json`, body:
  ```json
  {"rows": [{"<arg_name>": <value>, ...}, ...]}
  ```
- **Response** — HTTP `200` with body:
  ```json
  {"values": [<row_0_result>, <row_1_result>, ...]}
  ```
- `values.length` **must** equal `rows.length`. A mismatch is treated as an execution error.

**Scalar functions with dynamic table arguments** and **table functions** (`kind: table`) issue a single request per invocation with both scalar arguments and table inputs:

- **Request body**:
  ```json
  {"args": {"<arg_name>": <value>, ...}, "tables": {"<table_name>": [{"<col>": <value>, ...}, ...]}}
  ```
  The `tables` field is omitted when no table inputs are declared. Each row in a table input is a JSON object whose keys match the declared `tables[].columns`.
- **Response body** — `{"rows": [{"<output_col>": <value>, ...}, ...]}` for table functions, `{"values": [<result>]}` for scalar.

Non-`2xx` responses surface as query errors, with the response body snippet (up to 256 chars, one line) included in the error for debugging. Argument and return values are encoded through Arrow's JSON reader/writer, so any Arrow type with a JSON representation (including `list<…>`, `struct<…>`, and timestamps) is permitted.

#### Remote params

| Key                  | Type                                                   | Default | Description                                                                                              |
| -------------------- | ------------------------------------------------------ | ------- | -------------------------------------------------------------------------------------------------------- |
| `timeout`            | integer seconds **or** duration string (`2s`, `500ms`) | `30s`   | Per-call HTTP timeout.                                                                                   |
| `batch_size`         | integer, `1..=100000`                                  | `1024`  | Rows per HTTP request for scalar functions. Large inputs are chunked.                                    |
| `batch_concurrency`  | integer, `1..=64`                                      | `4`     | Maximum in-flight HTTP batches per invocation. Results are appended in input order.                      |
| `max_response_bytes` | integer bytes, up to `1 GiB`                           | `10MiB` | Maximum decoded response body size per HTTP call. Larger responses fail the query.                       |
| `max_rows`           | integer, up to `1000000`                               | `100000`| Cap on rows returned by a table-function call when the query has no `LIMIT`. Ignored for scalar batches. |
| `auth_bearer`        | string (supports `${secrets:…}`)                       | none    | Sets `Authorization: Bearer <value>` on every request.                                                   |
| `allowed_endpoint_ranges` | list of CIDR strings                              | `[]`    | SSRF egress allowlist. The remote tier blocks requests that resolve to non-public or cloud-metadata IP ranges; add CIDRs here to explicitly permit specific internal ranges. Resolved IPs are checked before connecting. |

### WebAssembly (`from: wasm`)

{% hint style="info" %}
The WASM tier requires the `wasm-functions` cargo feature (compiling Rust source additionally requires `wasm-functions-compile`). It is enabled by default in Spice.ai Enterprise and Spice Cloud Platform distributions. Open source users must build locally to enable it. See [Distributions](../getting-started/distributions.md).
{% endhint %}

The WebAssembly tier executes user code inside a sandboxed [wasmtime](https://wasmtime.dev/) module, exchanging Arrow IPC streams with the host. Both scalar and table kinds are supported; a single guest export serves both shapes.

```yaml
- name: tokenize_lines
  from: wasm
  kind: table
  signature:
    tables:
      - name: input
        columns: [{ name: doc, type: utf8 }]
    returns:
      - { name: token, type: utf8 }
      - { name: position, type: int64 }
  params:
    module: ./modules/tokenizer.wasm
    entrypoint: spice_transform     # optional, defaults to spice_transform
    fuel: 100000000                 # default
    max_memory_bytes: 67108864      # default 64 MiB
```

#### WebAssembly params

| Key                  | Type   | Default            | Description                                                                                              |
| -------------------- | ------ | ------------------ | -------------------------------------------------------------------------------------------------------- |
| `module`             | string | —                  | Path to a precompiled `.wasm` artifact. Mutually exclusive with `source`. One of the two is required.    |
| `source`             | string | —                  | Path to source code to compile to WASM at registration. Requires `wasm-functions-compile`.               |
| `language`           | string | `rust`             | Source language when `source` is set. Only `rust` is supported today.                                    |
| `entrypoint`         | string | `spice_transform`  | Exported guest function name.                                                                            |
| `input_table`        | string | —                  | Name of an existing table to use as the table input. Mutually exclusive with `body` / `body_ref`.        |
| `fuel`               | integer| `100000000`        | Per-invocation wasmtime fuel budget. Exhaustion fails the call with an interrupt error.                  |
| `max_memory_bytes`   | integer| `67108864` (64MiB) | Hard cap on guest linear-memory growth.                                                                  |
| `max_table_elements` | integer| `1000000`          | Hard cap on guest WASM-table growth.                                                                     |

The guest module must export `memory`, `spice_alloc`, `spice_dealloc`, and the configured entrypoint. The entrypoint receives `(input_ptr, input_len, args_ptr, args_len)` and returns a packed `(output_ptr << 32) | output_len` pointer to an Arrow IPC stream matching the declared return schema.

## Table Functions

Set `kind: table` to register a user-defined table function (UDTF). Table functions return a relation instead of a single value and are invoked in a SQL `FROM` clause:

```sql
SELECT * FROM split_lines('hello\nworld');
```

The `signature.returns` field becomes a list of output columns. The function may take any combination of scalar arguments and declared `signature.tables` inputs; table inputs always precede scalar arguments at call sites.

### SQL table functions

The body is a single `SELECT` query. Scalar arguments are visible through the reserved `args` table; declared `signature.tables` inputs are visible by their declared names.

```yaml
functions:
  - name: emit_pair
    from: sql
    kind: table
    volatility: immutable
    signature:
      args: [{ name: x, type: int64 }]
      returns:
        - { name: value,   type: int64 }
        - { name: doubled, type: int64 }
    body: |
      SELECT x AS value, x * 2 AS doubled FROM args
      UNION ALL
      SELECT x + 1 AS value, (x + 1) * 2 AS doubled FROM args
```

```sql
SELECT * FROM emit_pair(4);
-- value | doubled
--   4   |    8
--   5   |   10
```

To pass a relation into a table function, declare a `signature.tables` entry and reference the input table inside the body. The argument at the call site can be a table name or an inline subquery:

```yaml
functions:
  - name: scale_values
    from: sql
    kind: table
    signature:
      tables:
        - name: input
          columns: [{ name: value, type: int64 }]
      args: [{ name: factor, type: int64 }]
      returns:
        - { name: scaled, type: int64 }
    body: |
      SELECT input.value * args.factor AS scaled
      FROM input CROSS JOIN args
```

```sql
SELECT * FROM scale_values(numbers, 3);
SELECT * FROM scale_values((SELECT value FROM numbers WHERE value > 0), 3);
```

### Remote and WebAssembly table functions

Remote (`from: http://…`) and WebAssembly (`from: wasm`) table functions follow the same `kind: table` shape. Inputs and outputs are exchanged over the tier's native wire format — JSON for remote, Arrow IPC for WASM — as described in [Execution Tiers](#execution-tiers).

Table functions are always SQL-only. They are not exposed to LLM tool-calling regardless of the `as_tool` setting.

## Supported Types

All tiers share a single Arrow type parser. Type names are case-insensitive and accept shorthand aliases (`string` ↔ `utf8`, `bool` ↔ `boolean`, `int` ↔ `int32`, `double` ↔ `float64`).

| Arrow type                                                                  | Notes                                                                           |
| --------------------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| `int8`, `int16`, `int32`, `int64`                                           |                                                                                 |
| `uint8`, `uint16`, `uint32`, `uint64`                                       |                                                                                 |
| `float32`, `float64`                                                        |                                                                                 |
| `utf8` / `string`, `large_utf8`                                             |                                                                                 |
| `boolean` / `bool`                                                          |                                                                                 |
| `binary`, `large_binary`                                                    |                                                                                 |
| `date32`, `date64`                                                          |                                                                                 |
| `timestamp(s)`, `timestamp(ms)`, `timestamp(us)`, `timestamp(ns)`           | Optional second argument for a timezone, e.g. `timestamp(us, utc)`.             |
| `decimal(p, s)`, `decimal128(p, s)`, `decimal256(p, s)`                     |                                                                                 |
| `list<T>`, `large_list<T>`                                                  |                                                                                 |
| `struct<name:T, name2:T2, …>`                                               | Field names may be unquoted or quoted (`"name":T` / `'name':T`).                |

Return types are coerced implicitly where DataFusion knows how (e.g. `Int32 → Int64`, `Float32 → Float64`, `Utf8 → LargeUtf8`) — a literal like `6371` widening to `Float64` is fine.

**Tier notes:**

- **SQL tier** lowers the body to a DataFusion physical expression, so any DataFusion-recognised type round-trips natively.
- **Remote tier** serializes arguments and results through Arrow's JSON reader/writer, so any Arrow type with a JSON representation is acceptable. Plan for wire size when passing nested structures or large lists.
- **WebAssembly tier** uses Arrow IPC streams as the host/guest ABI; the guest module must understand the declared input and output schemas.

## Volatility

Volatility governs caching, constant-folding, and plan-time evaluation. Defaults to `volatile` — the safest choice.

| Value       | Meaning                                                                 | Safe to                       |
| ----------- | ----------------------------------------------------------------------- | ----------------------------- |
| `immutable` | Same inputs always yield the same output (e.g. `abs`, `upper`).         | Constant-fold, cache forever. |
| `stable`    | Stable within a single query, may change across queries (e.g. `now()`). | Cache per query.              |
| `volatile`  | Unpredictable on every call (e.g. `random()`).                          | Never cache.                  |

{% hint style="info" %}
**Remote tier auto-caps at `stable`.** A user-declared `immutable` or `stable` remote function is lowered to DataFusion's `Stable` volatility — constant-folding is disabled because the runtime cannot prove the remote service is deterministic or stable across process lifetimes. Queries within a single execution still benefit from common-subexpression elimination.
{% endhint %}

## Federation Deny-List

Every user function is automatically added to Spice's federation deny-list. This prevents the query planner from attempting to push a user function down into a federated data source (e.g. Postgres, MySQL, Databricks) — the remote source doesn't know about it, and a pushdown would either fail or, worse, silently call a different function with the same name.

Built-in Spice UDFs (`ai`, `embed`, `bucket`, identity SQL functions, etc.) are likewise denied. Functions are added on registration and removed on deregistration / hot-reload removal. No manual configuration is required.

## Hot-Reload

When the spicepod file changes on disk, Spice diffs the `functions:` section against the live set:

- **Removed** functions are deregistered from DataFusion, removed from the deny-list, removed from `USER_FUNCTION_INFO`, and dropped from the tool registry.
- **Changed** functions are deregistered and re-registered (equality is structural — any field change triggers a re-register).
- **Added** functions are built and registered.

Failures to build a new or changed function are logged at `ERROR` and do not halt the diff — other functions still apply.

## Introspection

### SQL: `list_udfs()`

A built-in UDTF returns every function registered in the current session — Spice built-ins, DataFusion standard library, and user-declared functions together.

```sql
SELECT name, source, kind, volatility, "from", description
FROM list_udfs()
WHERE source = 'user';
```

| Column        | Type             | Description                                                                 |
| ------------- | ---------------- | --------------------------------------------------------------------------- |
| `name`        | `utf8`, NOT NULL | UDF identifier.                                                             |
| `source`      | `utf8`, NOT NULL | `"builtin"` or `"user"`.                                                    |
| `kind`        | `utf8`, nullable | `"scalar"` \| `"aggregate"` \| `"window"` \| `"table"`. NULL for built-ins. |
| `volatility`  | `utf8`, nullable | `"immutable"` \| `"stable"` \| `"volatile"`. NULL for built-ins.            |
| `from`        | `utf8`, nullable | Source URI for user functions. NULL for built-ins.                          |
| `description` | `utf8`, nullable | Free-form description.                                                      |

### HTTP: `GET /v1/functions`

Returns only the user-declared functions (not built-ins). Intended for operational tooling.

```bash
curl -H "Authorization: Bearer $API_KEY" http://localhost:8090/v1/functions
```

```json
[
  {
    "name": "haversine_km",
    "kind": "scalar",
    "volatility": "immutable",
    "from": "sql",
    "description": "Haversine distance in kilometres"
  }
]
```

The OpenAPI spec exposes this under `operation_id: list_functions`, tag `Functions`.

## LLM Tool Exposure

When `as_tool: true` (the default), every declared **scalar** function is also registered as an LLM tool. The tool invokes the function through a `SELECT fn_name(arg0, …) AS result` query over the runtime's DataFusion session, so all execution tiers dispatch transparently. Table functions (`kind: table`) are always SQL-only and are not surfaced to the LLM.

- Argument values are interpolated as **typed SQL literals** by the tool bridge — integers, floats, strings, booleans. No free-form SQL from the model is ever concatenated.
- The tool's JSON Schema `parameters` is derived from the function's Arrow signature. Types outside the JSON-encodable primitive set (`int64`, `float64`, `utf8`, `boolean`) are not eligible for tool exposure — the function still registers for SQL use, but tool registration is skipped with a `WARN` log.
- Name collisions with a previously-registered tool (built-in or `tools:` entry) are skipped with a `WARN` log. Rename one, or set `as_tool: false` on the function.

```yaml
# SQL-callable only — not surfaced to the LLM.
- name: internal_hash
  from: sql
  signature: { args: [{ name: x, type: int64 }], returns: int64 }
  body: "x * 2654435761"
  as_tool: false
```

## Exposing Tools as SQL UDFs

The inverse of [LLM Tool Exposure](#llm-tool-exposure): set `as_sql: true` on a `tools:` entry to register the tool as a SQL scalar UDF. The UDF dispatches to the tool per row, packing each row's arguments into a JSON object that matches the supplied signature.

```yaml
runtime:
  functions:
    enabled: true

tools:
  - name: geocode
    from: http://geocode.internal/v1/lookup
    as_sql: true
    signature:
      args: [{ name: address, type: utf8 }]
      returns: utf8
```

```sql
SELECT geocode(address) FROM addresses;
```

### Tool fields for SQL exposure

| Field       | Type      | Description                                                                                                                                                            |
| ----------- | --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `as_sql`    | bool      | Defaults to `false`. Set to `true` to register a scalar UDF alongside the LLM tool. Requires `runtime.functions.enabled: true` and a `signature:` block.               |
| `signature` | object    | Typed [signature](#signature) for SQL invocation. Required when `as_sql: true`; ignored otherwise.                                                                     |

### Behaviour

- A `signature:` block is required when `as_sql: true` — a tool's free-form JSON Schema does not uniquely map to a SQL signature, so argument and return Arrow types must be pinned explicitly.
- Supported Arrow types are the same primitive set as the [Remote tier](#remote-tier): `int64`, `float64`, `utf8`, `boolean`. Other types are rejected at build time with an `Unsupported*Type` error naming the tool.
- The derived UDF is **always `volatile`**. Constant-folding is disabled and per-row results are not memoized because the underlying tool is non-deterministic RPC.
- Per-query dispatch is capped at **16 concurrent in-flight calls**, with row order preserved on output.
- Tool-backed UDFs are automatically added to the federation deny-list and are never pushed down to remote data sources.
- If `runtime.functions.enabled` is `false`, or `signature:` is missing, SQL registration is skipped with a `WARN` log and the tool remains callable as an LLM tool only.

## Distributed Execution

In a Spice.ai Enterprise [distributed cluster](distributed-query.md), UDFs declared on the scheduler are automatically propagated to executors as part of the `GetAppDefinition` bootstrap RPC — executors load the full Spicepod definition (datasets, catalogs, views, and UDFs) from the scheduler at startup, so query planning and execution use a consistent view of the function set across all nodes.

User functions are deny-listed from federation pushdown on every node, so there's no risk of a stray executor attempting to push a user function into a downstream source.

## Limitations

Current (ALPHA) scope:

- `kind: scalar` and `kind: table` are implemented. Any other `kind:` value (e.g. `aggregate`, `window`) is rejected at parse time as an unknown variant and prevents the spicepod from loading.
- Supported `from:` schemes are `sql`, `http://`, `https://`, and `wasm`. The HTTP and WASM tiers are gated behind the `http-functions` and `wasm-functions` cargo features respectively (default-on in Enterprise and Cloud, opt-in for OSS builds). Other schemes (e.g. `grpc://`, `flight://`) parse but are rejected.
- LLM tool exposure (`as_tool: true`) is limited to scalar functions with primitive Arrow types in their signature (`int64`, `float64`, `utf8`, `boolean`). Functions with richer signatures remain callable from SQL.
- Remote tier issues batches in parallel with a default `batch_concurrency` of 4 (max 64). Retries, circuit-breakers, per-function rate limits, and `http-arrow` / Flight protocols are not yet implemented.
- No aggregate/window semantics, UDAF pushdown, or execution-engine delegation beyond what DataFusion's scalar UDF and table function paths provide.
