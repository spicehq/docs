---
icon: eye
---

# Publish

Publishing a project controls its visibility to other users on the Spice.ai platform.

## Project visibility

Projects can be set to one of two visibility levels:

- **Private** (default): Only organization members can view and access the project.
- **Public**: The project is visible to all Spice.ai users. Public projects can be discovered, queried, and used as data sources by others via the `spice connect` or `spice add` commands.

## Before publishing

A project must be connected to a **public** GitHub repository before it can be published. SpiceRack uses the repository to show the Spicepod configuration behind the public listing. See [Connect GitHub](connect-github.md).

Viewers cannot change project visibility.

## Changing visibility

1. Select your project.
2. Navigate to **Settings** and scroll to the **Project visibility** section.
3. Click **Publish project** to make it public, or **Make private** to unpublish it.
4. Confirm in the dialog.

{% hint style="warning" %}
Making a project public exposes its datasets and models to all Spice.ai users. Ensure no sensitive data is accessible before publishing.
{% endhint %}

Learn more about [public projects](../public-apps.md).
