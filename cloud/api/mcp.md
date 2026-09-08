---
description: Connect MCP clients and AI coding agents to a Spice project
icon: plug
---

# MCP API

The MCP API exposes a project's configured tools over the [Model Context Protocol](https://modelcontextprotocol.io/) using the Streamable HTTP transport. MCP clients — including AI coding agents — connect to the project's runtime, list the tools it serves, and call them to query the project's datasets and models.

{% hint style="info" %}
**Runtime Endpoint:** These endpoints are served by your Spice runtime instance (e.g. `https://<project-cname>.spiceai.io`), not the Management API (`https://api.spice.ai`). Authenticate with your **project API key**, not a Personal Access Token.
{% endhint %}

{% hint style="warning" %}
The MCP API requires Spice runtime **v2.0.0 or later**. See [Spice Runtime Versions](../portal/app-spicepod/spice-runtime-versions.md).
{% endhint %}

Which tools a project exposes depends on the `tools` configured in its spicepod. See [Model Context Protocol (MCP)](https://docs.spiceai.org/features/large-language-models/mcp) for tool configuration.

## Send an MCP message

<mark style="color:blue;">`POST`</mark> `https://<project-cname>.spiceai.io/v1/mcp`

Sends a JSON-RPC message to the project's MCP server.

### Headers

| Header           | Type   | Description                                                                                          |
| ---------------- | ------ | ---------------------------------------------------------------------------------------------------- |
| `X-API-Key`      | string | Your project API key                                                                                     |
| `Content-Type`   | string | `application/json`                                                                                   |
| `Accept`         | string | `application/json` for a single JSON-RPC response, or `text/event-stream` to receive an SSE stream    |
| `Mcp-Session-Id` | string | Session identifier returned by the server on `initialize`. Required on every request that follows it. |

### Responses

| Status | Description                                                                             |
| ------ | --------------------------------------------------------------------------------------- |
| `200`  | JSON-RPC response, returned as `application/json` or as `text/event-stream` when the server streams additional messages. |
| `202`  | Message accepted. Returned for notifications and responses that do not require a reply.  |
| `400`  | Malformed JSON-RPC payload.                                                             |
| `404`  | Unknown or expired `Mcp-Session-Id`.                                                    |
| `413`  | Payload too large. The maximum message size is 32 MiB.                                  |

```bash
curl -X POST "https://<project-cname>.spiceai.io/v1/mcp" \
  -H "X-API-Key: <project-api-key>" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json, text/event-stream" \
  -d '{
    "jsonrpc": "2.0",
    "id": 1,
    "method": "tools/list"
  }'
```

## Open a server-to-client stream

<mark style="color:blue;">`GET`</mark> `https://<project-cname>.spiceai.io/v1/mcp`

Opens a long-lived server-to-client SSE stream for an existing session, as defined by the Streamable HTTP transport.

### Headers

| Header           | Type   | Description                                                            |
| ---------------- | ------ | ---------------------------------------------------------------------- |
| `X-API-Key`      | string | Your project API key                                                       |
| `Mcp-Session-Id` | string | Session identifier of a session created by `POST /v1/mcp`. Required.    |

### Responses

| Status | Description                                                          |
| ------ | -------------------------------------------------------------------- |
| `200`  | SSE stream (`text/event-stream`) of server-originated MCP messages.   |
| `404`  | Unknown or expired `Mcp-Session-Id`.                                 |

## Connect an AI coding agent

The portal generates the registration command for a project. Open the project, select **Settings**, then **Endpoints**, and use **Connect an AI coding agent**.

Keep the project API key in an environment variable rather than writing it into a command or a config file:

```bash
export SPICE_API_KEY="<project-api-key>"
```

### Claude Code

```bash
claude mcp add --transport http spice "https://<project-cname>.spiceai.io/v1/mcp" \
  --header "X-API-Key: $SPICE_API_KEY"
```

### Codex

`codex mcp add` registers stdio servers only, so declare the Streamable HTTP server in `~/.codex/config.toml`. The `env_http_headers` value names the environment variable to read, not the key itself:

```toml
[mcp_servers.spice]
url = "https://<project-cname>.spiceai.io/v1/mcp"
env_http_headers = { "X-API-Key" = "SPICE_API_KEY" }
```

### Grok CLI

```bash
grok mcp add --transport http spice "https://<project-cname>.spiceai.io/v1/mcp" \
  --header "X-API-Key: $SPICE_API_KEY"
```

{% hint style="info" %}
Spice Cloud projects need no `runtime.mcp.allowed_hosts` configuration. The project's own hostname is allow-listed when the project is deployed, and any entries set in the spicepod for local development are kept.
{% endhint %}

See also:

* [MCP Server recipe](https://github.com/spiceai/cookbook/tree/trunk/mcp-server) — using Spice as an MCP server.
* [Project API Keys](../portal/apps/api-keys.md) — creating and rotating project API keys.
