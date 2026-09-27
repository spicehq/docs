---
description: Add a dataset and query it using SQL Query in the Playground
icon: circle-3
---

# Add a Dataset and query data

To add a dataset to the Spice project, navigate to **Build** > [**Code**](../../portal/app-spicepod/).

Use the **Components sidebar** on the right to select from available **Data Connectors**, **Model Providers**, and ready-to-use **Datasets**.

### Adding a ready-to-use Dataset

1. Navigate to **Build** > **Code**.
2. In **Components** sidebar, click the **Datasets** tab.
3. Select and add the **NYC Taxi Trips** dataset
   1. Note the configuration has been added to the editor
4. Click **Save** in the code toolbar and then **Deploy** on popup card that appears in the bottom right.
5. Navigate to the [**Playground**](../../portal/playground/) tab, open the dataset reference, and click on the `spice.samples.taxi_trips` dataset to insert a sample query into the SQL editor. Then, click **Run Selection**.

### \[Optional] Execute a SQL query using cURL

6. Go to project **Settings** and copy one of the project API Keys.
7. Replace `[API-KEY]` in the sample below with your API Key and execute from a terminal.

{% tabs %}
{% tab title="cURL" %}
```sh
curl --request POST \
  --url 'https://data.spiceai.io/v1/sql' \
  --header 'Content-Type: text/plain' \
  --header 'X-API-KEY: [API-KEY]' \
  --data 'select * from spice.samples.taxi_trips limit 3'
```
{% endtab %}
{% endtabs %}

🎉 Congratulations, you've now added a dataset and queried it. That completes the getting started guide. No AI model or model provider API key is required.

Continue to [Next Steps](next-steps.md) to explore use-cases to do more with the Spice.ai Cloud Platform.

If you have an OpenAI, Anthropic (Claude), or xAI (Grok) API key, optionally [add an AI Model and chat with the dataset](step-3-add-ai-model-and-chat-with-your-app.md).

{% hint style="info" %}
Need help? Ask a question, raise issues, and provide feedback to the Spice AI team on [Slack](https://spiceai.org/slack).
{% endhint %}
