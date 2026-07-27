---
description: Query data, chat with models, and try the runtime APIs from the browser
icon: display-chart-up
---

# Playground

The **Playground** runs queries, searches, and model requests against an app from the browser, with no local setup. Open it by navigating to an app and clicking **Playground** in the app navigation bar.

Most of the Playground requires the app to have a spicepod. Until then, only **SQL Query** is available.

## Interactive tools

| Tool            | Description                                                                                              |
| --------------- | -------------------------------------------------------------------------------------------------------- |
| **SQL Query**   | A SQL editor with table, column, and keyword suggestions. See [SQL Query](sql-query-editor.md).           |
| **Text-to-SQL** | Ask a question in natural language and review the SQL Spice generates. See [Text-to-SQL](nsql-query.md).  |
| **AI Chat**     | Chat with a model configured in the app, with access to its datasets. See [AI Chat](ai-chat.md).          |
| **Search**      | Run search queries against the app's datasets.                                                           |

## APIs

The **APIs** section provides a console for each runtime API. Requests are built from the options on the page and sent to the app's own endpoint using its API key, and a matching `curl` command can be copied for use outside the Portal.

* **Query** — SQL over HTTP.
* **Search** — search over the app's datasets.
* **AI Completions** and **AI Responses** — model inference.
* **NSQL** and **NSQL Context** — natural-language querying, and the context block Spice supplies to the model. See [Text-to-SQL API](../../api/nsql.md).
* **Metrics** — runtime metrics in Prometheus format. See [Metrics API](../../api/metrics.md).
* **Control-Plane** — app and deployment operations.

See the [API Reference](../../api/) for the full contract behind each one.

## SDKs & CLI

The **SDKs & CLI** section generates connection snippets pre-filled with the app's endpoint, for **C#**, **Go**, **Java**, **Node.js**, **Python**, and **Rust**, plus the **Spice CLI** and **cURL**.
