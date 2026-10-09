---
icon: trash
---

# Delete Project

You can permanently delete a Spice project from the portal. Deleting a project will stop all running deployments and release its associated resources.

{% hint style="danger" %}
Deleting a project is **permanent** and cannot be undone. All associated deployments, datasets, and configurations will be removed.
{% endhint %}

## Delete a project

1. Navigate to the project you want to delete.
2. Click **Settings** in the project navigation sidebar.
3. Scroll down to the **Danger Zone** section.
4. Click the **Delete project** button.
5. In the **Delete Project** dialog, type `<org-name>/<project-name>` to confirm deletion.
6. Click **Delete** to permanently delete the project.

{% hint style="info" %}
Only project owners and organization admins can delete projects.
{% endhint %}

## Delete via API

Projects can also be deleted programmatically. See the [Management APIs](../../api/management/) reference for details.
