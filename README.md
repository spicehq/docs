---
description: Welcome to the Spice.ai Cloud Platform!
icon: hand-wave
layout:
  width: default
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: false
  pagination:
    visible: true
  metadata:
    visible: false
  tags:
    visible: true
---

# Welcome to Spice.ai

The **Spice.ai Cloud Platform** is an AI application and agent cloud — an AI-backend-as-a-service with composable, ready-to-use building blocks including high-speed SQL query, LLM inference, vector search, and RAG, built on cloud-scale, managed [Spice.ai OSS](https://github.com/spiceai/spiceai).

{% hint style="info" %}
This documentation covers the **Spice.ai Cloud Platform**.

For the self-hostable **Spice.ai OSS** runtime, visit [**docs.spiceai.org**](https://docs.spiceai.org).
{% endhint %}

<table data-view="cards"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><strong>🚀</strong></td><td><strong>Get Started</strong></td><td>Sign up and run your first query in minutes</td><td><a href="cloud/getting-started/get-started/">get-started</a></td></tr><tr><td><strong>⚡</strong></td><td><strong>Federated SQL Query</strong></td><td>Query across any data source with one SQL interface</td><td><a href="features/federated-sql-query.md">federated-sql-query.md</a></td></tr><tr><td><strong>🤖</strong></td><td><strong>AI Gateway</strong></td><td>OpenAI-compatible API for LLM inference</td><td><a href="features/ai-gateway.md">ai-gateway.md</a></td></tr><tr><td><strong>🔍</strong></td><td><strong>Search &#x26; Retrieval</strong></td><td>Vector and hybrid search for RAG workflows</td><td><a href="features/search-and-retrieval.md">search-and-retrieval.md</a></td></tr><tr><td><strong>🔌</strong></td><td><strong>Data Connectors</strong></td><td>Connect to 30+ databases, warehouses, and lakes</td><td><a href="building-blocks/data-connectors/">data-connectors</a></td></tr><tr><td><strong>📊</strong></td><td><strong>Monitoring</strong></td><td>Observe performance with Grafana, Datadog, and more</td><td><a href="/docs/monitoring/monitoring">monitoring</a></td></tr></tbody></table>

## What You Can Do

With the **Spice.ai Cloud Platform** you can:

* **Query and accelerate data** — Run high-performance SQL queries across multiple data sources with results optimized for AI applications and agents.
* **Use AI models** — Perform LLM inference with OpenAI, Anthropic, xAI, and more for chat, completion, and generative AI workflows.
* **Build agentic AI apps** — Combine data, models, search, and tools into production-grade AI agent backends.
* **Collaborate on Spicepods** — Share, fork, and manage datasets, models, embeddings, evals, and tools in a collaborative hub indexed by [spicerack.org](https://spicerack.org).

## Use Cases

| Use Case                                            | Description                                                |
| --------------------------------------------------- | ---------------------------------------------------------- |
| [Agentic AI Apps](use-cases/agentic-ai-apps.md)     | Build AI agent backends with unified data and model access |
| [Database CDN](use-cases/database-cdn.md)           | Cache and accelerate hot data for low-latency applications |
| [Data Lakehouse](use-cases/data-lakehouse.md)       | Federated queries across warehouses, lakes, and databases  |
| [Enterprise Search](use-cases/enterprise-search.md) | Semantic search across enterprise data sources             |
| [Enterprise RAG](use-cases/enterprise-rag.md)       | Retrieval-augmented generation with your own data          |

{% columns %}
{% column %}
**Quick Start**

Get up and running in minutes:

1. [Sign in with GitHub](cloud/getting-started/get-started/portal-login.md)
2. [Create a Spice app](cloud/getting-started/getting-started/portal-login-1.md)
3. [Add a dataset and query data](cloud/getting-started/get-started/step-2-add-dataset-and-query-data.md)
4. [Add an AI model and chat](cloud/getting-started/get-started/step-3-add-ai-model-and-chat-with-your-app.md)

<a href="cloud/getting-started/get-started/" class="button primary" data-icon="rocket-launch">Get started</a> <a href="/api/runtime-apis/sql-query" class="button secondary" data-icon="terminal">API reference</a>
{% endcolumn %}

{% column %}
{% code title="query.py" overflow="wrap" %}
```python
from spicepy import Client

client = Client("YOUR_API_KEY")
reader = client.query(
    "SELECT * FROM my_table LIMIT 10"
)
df = reader.read_pandas()
print(df)
```
{% endcode %}
{% endcolumn %}
{% endcolumns %}

## Community & Support

* **Slack** — Ask questions and get help from the team at [spice.ai/slack](https://spice.ai/slack).
* **GitHub** — File issues and contribute at [github.com/spiceai/spiceai](https://github.com/spiceai/spiceai).
* **Enterprise support** — [Paid plans](/docs/pricing/plans) include priority support with an SLA.
* **Help Center** — Browse the [Help Center](/help) for troubleshooting, guides, and FAQs.
