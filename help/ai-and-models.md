---
icon: robot
description: Use the AI Gateway for LLM inference, embeddings, and search across multiple model providers.
---

# AI & Models

## Overview

Spice.ai provides an **OpenAI-compatible AI Gateway** that lets you access multiple model providers through a unified API. This enables LLM inference, embeddings, vector search, and RAG workflows.

See [AI Gateway](/features/ai-gateway) for full feature details.

## Supported Model Providers

| Provider     | Documentation                                                     |
| ------------ | ----------------------------------------------------------------- |
| OpenAI       | [OpenAI](/building-blocks/model-providers/openai)            |
| Anthropic    | [Anthropic](/building-blocks/model-providers/anthropic)      |
| Azure OpenAI | [Azure](/building-blocks/model-providers/azure)              |
| xAI (Grok)   | [XAI](/building-blocks/model-providers/xai)                  |
| Hugging Face | [Hugging Face](/building-blocks/model-providers/huggingface) |
| Perplexity   | [Perplexity](/building-blocks/model-providers/perplexity)    |
| Spice.ai     | [SpiceAI](/building-blocks/model-providers/spiceai)          |

See [Model Providers](/building-blocks/model-providers/) for the complete list.

## Setting Up AI in Your App

### 1. Add a model provider secret

Store your model provider API key as a [secret](/docs/portal/apps/secrets) in your app (e.g., `OPENAI_API_KEY`).

### 2. Configure a model in your Spicepod

```yaml
models:
  - from: openai:gpt-4o
    name: my_model
    params:
      openai_api_key: "${secrets:OPENAI_API_KEY}"
```

### 3. Deploy

Deploy your app to make the model available.

### 4. Use the API

Send requests to the [LLM API](/api/runtime-apis/openai-api):

```bash
curl https://data.spiceai.io/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "X-API-Key: YOUR_API_KEY" \
  -d '{
    "model": "my_model",
    "messages": [{"role": "user", "content": "Hello!"}]
  }'
```

## Key Features

### OpenAI-compatible API

The AI Gateway exposes an OpenAI-compatible interface at `https://data.spiceai.io/v1/chat/completions`. You can use any OpenAI-compatible client library — just point it at the Spice.ai endpoint and use your app's API key.

### Custom tools & system prompts

Configure custom tools and system prompts in your model configuration to tailor AI behavior. See [AI Gateway](/features/ai-gateway) for configuration options.

### Vector search & RAG

Spice supports [vector and hybrid search](/features/search-and-retrieval) for retrieval-augmented generation (RAG) workflows:

- Configure [embeddings](/building-blocks/embeddings/) for your datasets.
- Use the [Search API](/api/runtime-apis/search) for semantic search.

### Observability

All AI requests include full [OpenTelemetry observability](/features/observability/) for tracing request flows, latency, and errors.

## Common Issues

### AI chat returns errors

1. **Model not configured** — Ensure a model is defined in your Spicepod and the app is deployed.
2. **Missing secret** — Verify the model provider API key is stored as a secret and referenced correctly with `${secrets:SECRET_NAME}`.
3. **Secret changes require redeployment** — After adding or updating secrets, redeploy the app.

### Model not found

- Check that the model name in your API request matches the `name` field in your Spicepod model configuration.
- Verify the `from` field uses a valid provider and model identifier.

### Rate limits or quota errors

These typically come from the upstream model provider (e.g., OpenAI). Check your provider's usage dashboard and rate limits.

## Further Reading

- [AI Gateway](/features/ai-gateway)
- [LLM API reference](/api/runtime-apis/openai-api)
- [Search & Retrieval](/features/search-and-retrieval)
- [Model Providers](/building-blocks/model-providers/)
- [Semantic Models](/features/semantic-models)
