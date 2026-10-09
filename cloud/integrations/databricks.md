---
icon: plug
---

# Databricks

{% hint style="info" %}
Databricks OAuth connections require Spice.ai v1.4.0 or higher.
{% endhint %}

### Create Databricks App Connection

To connect Spice Cloud to your Databricks workspace, create a new App connection in your workspace settings with the following configuration:

* **Application Name**: `Spice Cloud Platform`
* **Redirect URLs**: `https://spice.ai/api/integrations/databricks/callback`
* **Access scopes**: `All Apis`
* **Client secret generation:** `disabled`

After creating the connection, copy the generated OAuth client ID:

### Configure Databricks Connection in Spice Cloud

Navigate to the code editor and select the Databricks connector. Choose either SQL Warehouse or Delta Lake mode, then select the OAuth authorization option. Click **Connect Databricks Workspace** to proceed.

Enter the workspace URL and OAuth Client ID generated from the Databricks OAuth app, then click **Connect**.

After successful authentication in Databricks, you will be redirected back to the Spice app:

{% hint style="info" %}
Note: Databricks authentication credentials are only stored client-side in your browser and never in Spice Cloud.
{% endhint %}

Once connected, Databricks datasets, catalogs, and models can use the connection:
