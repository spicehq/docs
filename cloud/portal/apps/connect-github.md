---
description: Connect your Spice.ai project to a GitHub repository
icon: link-simple
---

# Connect GitHub

## Prepare the repository

Before connecting:

* **Admin access**: Ensure you have administrative access to the GitHub repository. This level of access is required to [install the Spice.ai GitHub app](https://docs.github.com/en/apps/using-github-apps/installing-a-github-app-from-github-marketplace-for-your-organizations#requirements-to-install-a-github-app-on-an-organization).
* **Matching repository name**: The Spice.ai project and the GitHub repository names **must match**. For example:
  * **Spice.ai project**: [spice.ai/spiceai/demo](https://spice.ai/spiceai/demo)
  * **GitHub repository**: [github.com/spiceai/demo](https://github.com/spiceai/demo)

To quickly set up a new repository, use the [spiceai/spicepod-template](https://github.com/spiceai/spicepod-template) as a starting point:

{% hint style="warning" %}
Make sure to copy the project spicepod.yaml contents from **Build** > **Code** and place it in the root of the repository before linking.
{% endhint %}

## Connect

1. Ensure the repository is set up as per instructions above.
2. In the context of the Spice project to connect, navigate to **Settings**, then find the **Connect Git Repository** section.
3. Follow GitHub App installation instructions.

> Ensure that you select all repositories or specifically the repository you intend to connect.

4. Finally, link the repository to your Spice.ai project.

Once connected, the section is titled **Connected Git Repository** and offers a **Production Branch** selector and a **Disconnect** button.
