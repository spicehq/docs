---
description: Ask a question in natural language and review the SQL Spice generates
icon: comment-code
---

# Text-to-SQL

**Text-to-SQL** turns a natural language question into a SQL query, runs it, and shows both the query and its results. The generated SQL stays visible and editable, so it can be checked and corrected before being relied on.

Open it by navigating to a project's **Playground** and clicking **Text-to-SQL** in the sidebar.

## Prerequisites

The project must have a model with tools support configured in its spicepod — Text-to-SQL relies on the model's access to dataset schemas to generate a query. When no suitable model is configured, the page prompts to configure one instead.

To add a model, see [Spicepod Configuration](../app-spicepod/spicepod-configuration.md) and set `tools: auto` in the model's `params`, as in the [AI Chat](ai-chat.md) example.

## Generating a query

1. Select a model. The first available model is selected automatically.
2. Type a question, for example `Show the top 10 trips by distance`.
3. Optionally enable **Sample data** to include sample values from the datasets in the context sent to the model. This is off by default.
4. Click **Generate & Run**.

The generated SQL appears in the editor and runs automatically, with results in the panel below. The SQL can then be edited and re-run, as in the [SQL Query](sql-query-editor.md) editor.

{% hint style="info" %}
Enabling **Sample data** sends sample values from the project's datasets to the configured model provider. Leave it off when working with sensitive data.
{% endhint %}

## Using the API

The same capability is available outside the Portal through `POST /v1/nsql`, and the context Spice supplies to the model can be retrieved with `GET /v1/nsql/context`. See the [Text-to-SQL API](../../api/nsql.md).
