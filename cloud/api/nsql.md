---
description: Generate and run SQL from a natural language question
icon: comment-code
---

# Text-to-SQL API

The Text-to-SQL API converts a natural language question into SQL, runs it against the app's datasets, and returns the result. A companion endpoint returns the **context block** — the dataset schemas, SQL dialect details, and function list that Spice supplies to the model — so the same information can be inspected or reused elsewhere.

{% hint style="info" %}
**Runtime Endpoint:** These endpoints are served by your Spice runtime instance (e.g. `https://<app-cname>.spiceai.io`), not the Management API (`https://api.spice.ai`). Authenticate with your **app API key**, not a Personal Access Token.
{% endhint %}

A model capable of SQL generation must be configured in the app's spicepod. When the request omits `model`, Spice uses the single compatible model configured in the spicepod; if none or more than one is configured, the request fails and the model must be named explicitly.

## Generate and run SQL

<mark style="color:blue;">`POST`</mark> `https://<app-cname>.spiceai.io/v1/nsql`

### Headers

| Header         | Type   | Description        |
| -------------- | ------ | ------------------ |
| `X-API-Key`    | string | Your app API key   |
| `Content-Type` | string | `application/json` |

### Body

| Field                 | Type       | Default | Description                                                                                                                          |
| --------------------- | ---------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| `query`               | string     | —       | The natural language question. Required.                                                                                             |
| `model`               | string     | —       | The model used for SQL generation. When omitted, the single compatible model configured in the spicepod is used.                      |
| `stream`              | boolean    | `false` | Stream the response instead of waiting for completion.                                                                               |
| `sample_data_enabled` | boolean    | `false` | Include sample data in the context supplied to the model.                                                                            |
| `datasets`            | string\[]  | —       | Datasets to sample from when building the model context. A sampling hint only — it does not restrict which tables a query can target. |
| `prompt_cache_key`    | string     | —       | A stable key forwarded to the model for provider-specific prompt caching.                                                            |

```bash
curl -X POST https://<app-cname>.spiceai.io/v1/nsql \
  -H "X-API-Key: <app-api-key>" \
  -H "Content-Type: application/json" \
  -d '{
    "query": "How many taxi trips were there last month?",
    "sample_data_enabled": true
  }'
```

## Get the SQL generation context

<mark style="color:blue;">`GET`</mark> `https://<app-cname>.spiceai.io/v1/nsql/context`

Returns the context block Spice injects into `/v1/nsql` model requests: the in-scope dataset schemas, the SQL dialect and engine details, and the available function groups. Use it to inspect or tune what the model sees.

{% hint style="warning" %}
This endpoint requires Spice runtime **v2.1.0 or later**. Apps pinned to an earlier version return `404`. See [Spice Runtime Versions](../portal/app-spicepod/spice-runtime-versions.md).
{% endhint %}

### Headers

| Header      | Type   | Description                                                                                                              |
| ----------- | ------ | ------------------------------------------------------------------------------------------------------------------------ |
| `X-API-Key` | string | Your app API key                                                                                                         |
| `Accept`    | string | `text/markdown` (used when the header is absent), `text/plain`, or `application/json`. Any other value returns a `406`.   |

### Query parameters

| Parameter          | Type       | Default              | Description                                                                                     |
| ------------------ | ---------- | -------------------- | ----------------------------------------------------------------------------------------------- |
| `model`            | string     | —                    | The model whose dataset allowlist is used. When omitted, the single compatible model is used.    |
| `include_sampling` | boolean    | `false`              | Include distinct-value samples per column. Also accepts `sample_data_enabled`.                   |
| `sampling_limit`   | integer    | `3`                  | Maximum rows per distinct-value sample. Maximum `100`.                                          |
| `include_examples` | boolean    | `include_sampling`   | Include example rows. Defaults to whatever `include_sampling` is set to.                        |
| `examples_limit`   | integer    | `3`                  | Maximum example rows per dataset. Maximum `100`.                                                |
| `datasets`         | string     | all in scope         | Repeat the parameter to name several datasets. When omitted, every dataset in scope is included. |

```bash
curl -H "X-API-Key: <app-api-key>" \
  -H "Accept: application/json" \
  "https://<app-cname>.spiceai.io/v1/nsql/context?include_sampling=true&sampling_limit=5"
```

### Response

{% tabs %}
{% tab title="200: OK" %}
With `Accept: application/json`, the body contains:

| Field          | Description                                                                          |
| -------------- | ------------------------------------------------------------------------------------ |
| `context`      | The rendered context block injected into `/v1/nsql` model requests.                  |
| `instructions` | High-level SQL generation instructions.                                              |
| `sql`          | SQL engine and dialect details — `engine`, `version`, `dialect`, `parser`, `notes`.   |
| `datasets`     | In-scope datasets with schema, metadata, relationship, key, and index details.        |
| `functions`    | Available function groups, filtered to the app's query context.                       |
| `samples`      | Sample blocks, present when sampling or examples are requested.                       |

With `text/markdown` or `text/plain`, the body is the rendered context block itself.
{% endtab %}

{% tab title="400: Bad Request" %}
Returned when a named dataset does not exist, when a limit is set to `0` while its corresponding option is enabled, when a limit exceeds `100`, or when `model` is omitted and the spicepod has no compatible model — or more than one.
{% endtab %}

{% tab title="406: Not Acceptable" %}
The `Accept` header requested a type other than `text/markdown`, `text/plain`, or `application/json`.
{% endtab %}
{% endtabs %}

## In the Portal

Both endpoints have an interactive console in the Playground under **APIs** — **NSQL** and **NSQL Context**. The NSQL Context console builds the request from the options above and shows the returned block. See [Playground](../portal/playground/).
