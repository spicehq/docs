---
icon: key
---

# Organization Secrets

**Organization Secrets** are encrypted key-value pairs defined once for an organization and shared with the projects that need them. A shared credential — a warehouse password, a model provider API key — is stored in one place instead of being copied into every project that uses it.

An organization secret reaches a project only after it is linked to that project. Storing a secret at the organization level does not expose it to every project in the organization.

Once a secret is saved, its value cannot be read back through Spice Cloud. The portal lists only the secret name and when it was created or last updated.

### Access by role

The **Secrets** section is available to organization owners, admins, and members. Viewers cannot view or manage secrets, and the section is hidden from them.

| Role   | Organization secrets            |
| ------ | ------------------------------- |
| Owner  | Create, update, delete          |
| Admin  | Create, update, delete          |
| Member | Create, update, delete          |
| Viewer | No access                       |

Roles are assigned per organization member. [Learn more about organizations.](organizations.md)

### Create an organization secret

1. Select the organization from the project selector.
2. Navigate to the **Settings** tab and select the **Secrets** section.
3. Fill the **Name** and **Value** fields and click **Add**.

Secret names must start with a letter or an underscore and may contain only letters, numbers, and underscores, for example `GITHUB_TOKEN`.

### Update or delete a secret

Editing a secret replaces its value. The name cannot be changed — to rename a secret, create a new one and delete the old one.

Deleting a secret requires confirmation, and removes it from every project it is linked to.

### Link a secret to a project

1. Select the project.
2. Navigate to the **Settings** tab and select the **Secrets** section.
3. Under **Linked Organization Secrets**, select each secret the project should receive.
4. Click **Save linked secrets**.

Linked secrets are referenced in the Spicepod configuration exactly like project secrets, as `${secrets:<SECRET_NAME>}`:

```yaml
models:
  - from: openai:gpt-4o
    name: gpt-4o
    params:
      openai_api_key: ${secrets:OPENAI_API_KEY}
```

{% hint style="info" %}
When a project secret and a linked organization secret have the same name, the project secret takes precedence at runtime.
{% endhint %}

To apply a change to linked secrets, initiate a new Spicepod deployment. [Learn more about deployments.](app-spicepod/deployments.md)
