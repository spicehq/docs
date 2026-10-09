---
icon: square-code
---

# Spicepod Configuration

### Edit project spicepod not connected to GitHub

To update the Spice project spicepod.yaml, navigate to **Build** > **Code**. Use Components sidebar to add data connectors, model providers and preconfigured datasets, or manually edit spicepod.yaml in code editor.

After saving the spicepod changes, a new deployment must be triggered. Learn more about [Spicepod Deployments](deployments.md).

### Edit project spicepod connected to GitHub

If the Spice project is connected to a GitHub repository ([learn more](../apps/connect-github.md) about how to connect), the only way to update the Spicepod configuration is to edit the spicepod.yaml file in the root of your repository and push it to the default branch.

To apply the updated spicepod, a new deployment must be triggered. Learn more about [Spicepod Deployments](deployments.md).

The **Build** > **Code** view allows to explore and preview files in connected repository.
