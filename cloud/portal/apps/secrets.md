---
icon: lock
---

# Secrets

Project Secrets are key-value pairs that are passed to the Spice Runtime instance as environment secrets. Secrets are securely encrypted and accessible only through the project in which they were created. To share one secret across several projects, use an [organization secret](../organization-secrets.md) instead.

Once a secret is saved, its value cannot be retrieved through Spice Cloud. Editing a secret replaces its value; the name cannot be changed.

The **Secrets** section is available to organization owners, admins, and members. Viewers cannot view or manage secrets.

### Create a new secret

1. Select your project.
2. Navigate to **Settings** tab and select **Secrets** section.
3. Fill **Secret Name** and **Secret Value** fields and click **Add**.
4. Saved secrets can be referenced in the Spicepod configuration as\
   `${secrets:<SECRET_NAME>}`, for example:

```yaml
models:   
  - from: openai:gpt-4o
    name: gpt-4o
    params:
      openai_api_key: ${secrets:OPENAI_API_KEY}
```

5. To apply secrets, you must initiate a new spicepod deployment. [Learn more about deployments.](../app-spicepod/deployments.md)

Secret names must start with a letter or an underscore and may contain only letters, numbers, and underscores.

### Linked Organization Secrets

Secrets defined for the organization are listed under **Linked Organization Secrets**. Only the secrets selected and saved here are available to the project at runtime and in secret pickers. When a project secret and a linked organization secret have the same name, the project secret takes precedence.

[Learn more about organization secrets.](../organization-secrets.md)
