---
icon: trash
---

# Delete App

You can permanently delete a Spice app from the portal. Deleting an app will stop all running deployments and release its associated resources.

{% hint style="danger" %}
Deleting an app is **permanent** and cannot be undone. All associated deployments, datasets, and configurations will be removed.
{% endhint %}

## Delete an App

1. Navigate to the app you want to delete.
2. Click **Settings** in the app navigation sidebar.
3. Scroll down to the **Danger Zone** section.
4. Click the **Delete app** button.
5. Type the full app name to confirm deletion.
6. Click **Delete Application** to permanently delete the app.

{% hint style="info" %}
Only app owners and organization admins can delete apps.
{% endhint %}

## Delete via API

Apps can also be deleted programmatically. See the [Apps Management API](/api/management-api/management/apps#delete-app) for details.
