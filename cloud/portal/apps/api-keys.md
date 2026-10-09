---
icon: key
---

# API keys

Each Spice project has two pre-generated API keys, which can be used with [Spice SDKs](../../../sdks/), the [HTTP API](../../api/sql-query/http-api.md) or the [Apache Arrow Flight API](../../api/sql-query/apache-arrow-flight-api.md).

## View API Keys

1. Navigate to your Spice project in the [portal](https://spice.ai).
2. Click **Settings** in the project navigation sidebar.
3. Under the **General** section, locate the **API Key 1** and **API Key 2** fields.
4. Click on an API key field to copy its value to your clipboard.

## Regenerate an API Key

If an API key has been compromised or you need to rotate keys, you can regenerate individual keys. Regenerating a key **immediately invalidates** the previous key.

1. Navigate to your Spice project and click **Settings**.
2. Scroll to the **Danger Zone** section.
3. Click the **Regenerate key** button next to the key you want to rotate (**Regenerate API Key 1** or **Regenerate API Key 2**).
4. Confirm the regeneration when prompted.
5. Copy the new key and update it in your applications.

{% hint style="warning" %}
Regenerating an API key immediately invalidates the old key. Any applications using the old key will lose access. Use the two-key system to rotate keys without downtime: update your applications to use the secondary key first, then regenerate the primary key.
{% endhint %}

## Regenerate via API

API keys can also be regenerated programmatically. See the [Management API](../../api/management/) reference for details.
