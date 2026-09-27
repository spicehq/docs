---
description: Optional. Add an OpenAI, Anthropic, or xAI model and chat with the NYC Taxi Trips dataset.
icon: robot
---

# Add an AI Model and chat with your data (Optional)

{% hint style="info" %}
This step is optional and is not required to get started. It needs an API key from one of these providers:

* [OpenAI](https://platform.openai.com/): an OpenAI API Platform account and API key.
* [Anthropic](https://platform.claude.com/): a Claude Developer Platform account and API key.
* [xAI](https://console.x.ai/): an xAI account and API key.

Don't have a key? Skip to [Next Steps](next-steps.md).
{% endhint %}

Complete [Add a Dataset and query data](step-2-add-dataset-and-query-data.md) first so the model has a dataset to answer questions about.

### Adding a Model Provider

1. Navigate to **Build** > **Code**.
2. In **Components** sidebar, click **Model Providers** tab, and select the provider that matches your key: **OpenAI**, **Anthropic**, or **xAI**.
3. Enter the **Model name.**
4. Enter the **Model ID** for the provider, (e.g. `gpt-4o`, `claude-sonnet-4-6`, or `grok-4.6`).
5. Set the provider **API Key** secret
   1. API keys and other secrets are securely stored and encrypted. See [Secrets](../../portal/apps/secrets.md).
6. Insert `tools: auto` in the `params` section of the Model to automatically connect datasets to the model.\
   \
   The final Spicepod configuration in the editor should match the tab for your provider:

{% tabs %}
{% tab title="OpenAI" %}
```yaml
name: my-first-app
kind: Spicepod
version: v1beta1

datasets:
  - from: s3://spiceai-demo-datasets/taxi_trips/2024/
    name: samples.taxi_trips
    description: Taxi trips dataset from Spice.ai demo datasets.
    params:
      file_format: parquet

models:
  - from: openai:gpt-4o
    name: gpt-4o
    params:
      endpoint: https://api.openai.com/v1
      openai_api_key: ${secrets:OPENAI_API_KEY}
      tools: auto
```
{% endtab %}

{% tab title="Anthropic" %}
```yaml
name: my-first-app
kind: Spicepod
version: v1beta1

datasets:
  - from: s3://spiceai-demo-datasets/taxi_trips/2024/
    name: samples.taxi_trips
    description: Taxi trips dataset from Spice.ai demo datasets.
    params:
      file_format: parquet

models:
  - from: anthropic:claude-sonnet-4-6
    name: claude-sonnet-4-6
    params:
      anthropic_api_key: ${secrets:ANTHROPIC_API_KEY}
      tools: auto
```
{% endtab %}

{% tab title="xAI" %}
```yaml
name: my-first-app
kind: Spicepod
version: v1beta1

datasets:
  - from: s3://spiceai-demo-datasets/taxi_trips/2024/
    name: samples.taxi_trips
    description: Taxi trips dataset from Spice.ai demo datasets.
    params:
      file_format: parquet

models:
  - from: xai:grok-4.6
    name: grok-4-6
    params:
      xai_api_key: ${secrets:XAI_API_KEY}
      tools: auto
```
{% endtab %}
{% endtabs %}

See [OpenAI](../../../building-blocks/model-providers/openai.md), [Anthropic](../../../building-blocks/model-providers/anthropic.md), and [xAI](../../../building-blocks/model-providers/xai.md) for the full list of parameters for each provider.

7. Click **Save** in the code toolbar and then **Deploy** in the popup card that appears in the bottom right to deploy the changes.
8. Navigate to **Playground** and select **AI Chat** in the sidebar.
9. Ask a question about the NYC Taxi Trips dataset in the chat. For example:
   * "What datasets are available?"
   * "What is the average fare amount of a taxi trip?"

### \[Optional] Call chat completions API using cURL

10. Replace `[API-KEY]` in the sample below with the project API Key, and `[MODEL-NAME]` with the model `name` from the Spicepod (e.g. `gpt-4o`), then execute in a terminal.

{% tabs %}
{% tab title="cURL" %}
```sh
curl --request POST \
      --url 'https://data.spiceai.io/v1/chat/completions' \
      --header 'Content-Type: application/json' \
      --header 'X-API-KEY: [API-KEY]' \
      --data '{ "messages": [{ "role": "user", "content": "Hello!" }], "model": "[MODEL-NAME]" }'
```
{% endtab %}
{% endtabs %}

🎉 Congratulations, you've now added an AI model and can use it to ask questions of the NYC Taxi Trips dataset.

Continue to [Next Steps](next-steps.md) to explore use-cases to do more with the Spice.ai Cloud Platform.

{% hint style="info" %}
Need help? Ask a question, raise issues, and provide feedback to the Spice AI team on [Slack](https://spiceai.org/slack).
{% endhint %}
