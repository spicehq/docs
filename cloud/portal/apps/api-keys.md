---
icon: key
---

# API keys

Each Spice app has two pre-generated API keys, which can be used with [Spice SDKs](../../../sdks/), the [HTTP API](../../api/sql-query/http-api.md) or the [Apache Arrow Flight API](../../api/sql-query/apache-arrow-flight-api.md).

## View API Keys

1. Navigate to your Spice app in the [portal](https://spice.ai).
2. Click **Settings** in the app navigation sidebar.
3. Under the **General** section, locate the **API Key 1** and **API Key 2** fields.
4. Click on an API key field to copy its value to your clipboard.

## Regenerate an API Key

If an API key has been compromised or you need to rotate keys, you can regenerate individual keys. Regenerating a key **immediately invalidates** the previous key.

1. Navigate to your Spice app and click **Settings** -> **General**.
2. Click the **Regenerate** button next to the API key you want to rotate (**API Key 1** or **API Key 2**).
3. Confirm the regeneration when prompted.
4. Copy the new key and update it in your applications.

{% hint style="warning" %}
Regenerating an API key immediately invalidates the old key. Any applications using the old key will lose access. Use the two-key system to rotate keys without downtime: update your applications to use the secondary key first, then regenerate the primary key.
{% endhint %}

## Regenerate via API

API keys can also be regenerated programmatically. See the [Management API](../../api/management/) reference for details.
