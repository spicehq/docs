---
icon: screwdriver-wrench
description: Solutions to common issues with the Spice.ai Cloud Platform.
---

# Troubleshooting

## Authentication & API Keys

### API requests return `401 Unauthorized`

- Verify you're using the correct API key for your project. Each project has its own keys — find them in the [Portal](../cloud/portal/apps/api-keys.md) under your project's settings.
- If you recently regenerated a key, the old key is immediately invalidated. Update all clients to use the new key.
- Ensure the API key is passed correctly:
  - **HTTP API**: Include the `X-API-Key` header.
  - **Arrow Flight**: Pass the key as the `password` in the handshake.
  - **SDKs**: Set the key in the client configuration.

### API key rotation without downtime

Each project has **two API keys**. To rotate without downtime:

1. Regenerate **Key 2** (while all clients use Key 1).
2. Update all clients to use Key 2.
3. Regenerate **Key 1**.

See [API Keys](../cloud/portal/apps/api-keys.md) for details.

## SQL Queries

### Query returns `table not found`

- **Check identifier casing.** Spice normalizes unquoted SQL identifiers to lowercase (PostgreSQL-style). If your source table uses mixed-case names like `MyTable`, you must double-quote it in queries:

  ```sql
  SELECT * FROM "MyTable"
  ```

- Confirm the dataset is configured in your project's [Spicepod](../cloud/portal/app-spicepod/spicepod-configuration.md) and the project has been deployed.
- Ensure the data connector credentials (secrets) are correct and the source is accessible.

### Query times out or is slow

- **Use data acceleration.** Accelerated datasets are cached locally and queries run significantly faster. See [Data Acceleration](../features/data-acceleration/).
- **Use `LIMIT`** to reduce result set size during development.
- **Use Arrow Flight** instead of HTTP for large result sets — it has no row-count limits and streams results efficiently.
- **Apply `ORDER BY` with `LIMIT`/`OFFSET`** when retrieving large datasets in chunks. See [Best Practices](../cloud/best-practices.md).

### HTTP SQL API returns truncated results

The HTTP SQL API has row and timeout limits. If you're hitting these:

- Use the [Arrow Flight API](../cloud/api/sql-query/apache-arrow-flight-api.md) or an [SDK](../sdks/python-sdk/) for unlimited streaming results.
- Add `LIMIT` and `OFFSET` clauses to paginate results.

## Data Connectors

### Data connector fails to connect

- Verify the connection credentials are stored as [secrets](../cloud/portal/apps/secrets.md) in your project and referenced correctly in the Spicepod using `${secrets:SECRET_NAME}` syntax.
- Confirm the data source is network-accessible from Spice.ai Cloud (check firewalls, IP allowlists, VPC peering).
- Check the [Data Connectors](../building-blocks/data-connectors/) documentation for connector-specific configuration requirements.

### Data refresh fails with schema mismatch

Spice infers the dataset schema at deployment time. If the source schema has changed (columns added, removed, or types changed):

1. Redeploy the project to re-infer the schema.

The accelerated table will be re-initialized with the updated schema on redeployment.

### Object store connector: `file_format` errors

When connecting to object stores (S3, ABFS, etc.):
- **Folder paths** require an explicit `file_format` parameter (e.g., `parquet`, `csv`).
- **File paths** can auto-detect the format from the file extension.

## AI & Models

### AI chat returns errors or empty responses

- Confirm a [model is configured](../features/ai-gateway.md) in your project's Spicepod and the project is deployed.
- Ensure the model provider API key (e.g., OpenAI, Anthropic) is stored as a [secret](../cloud/portal/apps/secrets.md) and referenced correctly.
- After adding or changing a secret, **redeploy the project** — secret changes don't take effect until the next deployment.

### Model not available or not responding

- Check the model name matches a supported model from your configured [model provider](../building-blocks/model-providers/).
- Verify the provider's API is operational (e.g., check OpenAI's status page).
- Review request logs in the [Portal Monitoring](../cloud/portal/monitoring-and-request-logs.md) for error details.

## Secrets

### Secret value is not accessible after saving

This is by design. Secret values are **write-only** — they cannot be retrieved after saving for security reasons. To change a secret value:

1. Delete the existing secret.
2. Create a new secret with the same name and the updated value.
3. Redeploy the project.

### Changes to secrets not taking effect

Secret changes require a **new deployment** before they take effect. After updating secrets, trigger a new deployment in the [Portal](../cloud/portal/app-spicepod/deployments.md).

## Deployments

### Project is paused

On the [Community Plan](../cloud/pricing/community.md), projects that receive no API requests for 7 consecutive days are automatically paused. To restore:

1. Go to your project in the [Portal](https://spice.ai).
2. Resume the project.

[Paid plans](../cloud/pricing/plans.md) do not auto-pause.

## Arrow Flight

### TLS/SSL certificate errors on macOS or Windows

If you encounter certificate verification errors when connecting via Arrow Flight:

- Set the `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` environment variable to point to your system's CA bundle.
- On macOS: `export GRPC_DEFAULT_SSL_ROOTS_FILE_PATH=/etc/ssl/cert.pem`
- Or use an SDK that handles TLS configuration automatically.

## Still stuck?

- Search the [FAQ](../faq.md) for your question.
- Ask in [Slack](https://spice.ai/slack) for community support.
- [Enterprise plans](../cloud/pricing/plans.md) include priority support with an SLA.
