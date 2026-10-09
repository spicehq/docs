# OSS Documentation Sync Agent

This agent synchronizes building blocks documentation from the Spice OSS repository to the cloud documentation repository.

## Overview

The OSS documentation at `spiceai/docs` serves as the source of truth for building blocks documentation. This agent keeps the cloud documentation (`scp-docs/building-blocks`) in sync with the latest OSS changes while preserving cloud-specific formatting (GitBook).

## Source and Target

- **Source Repository:** https://github.com/spiceai/docs
- **Source Path:** `./website/docs/components/*`
- **Target Path:** `./building-blocks/*`

### Component Mapping

| OSS Path | Cloud Path |
|----------|------------|
| `website/docs/components/data-connectors/` | `building-blocks/data-connectors/` |
| `website/docs/components/catalogs/` | `building-blocks/catalogs/` |
| `website/docs/components/data-accelerators/` | `building-blocks/data-accelerators/` |
| `website/docs/components/embeddings/` | `building-blocks/embeddings/` |
| `website/docs/components/models/` | `building-blocks/model-providers/` |
| `website/docs/components/vectors/` | `building-blocks/vectors/` |
| `website/docs/components/views/` | `building-blocks/views/` |

## Workflow

### Step 1: Clone OSS Repository

Clone the OSS docs repository to a temporary location:

```bash
git clone --depth 1 https://github.com/spiceai/docs /tmp/spiceai-docs
```

### Step 2: Identify Changes

Compare files in the OSS `website/docs/components/` directory with corresponding files in `building-blocks/`. Look for:

1. **New files** in OSS that do not exist in cloud docs
2. **Modified files** where OSS content has changed
3. **Deleted files** in OSS (flag for manual review)

### Step 3: Transform and Sync

For each file that needs syncing, apply the transformation rules below before copying to the target location.

## Transformation Rules

### Frontmatter

**OSS Format (Docusaurus):**
```yaml
---
title: 'Databricks Data Connector'
sidebar_label: 'Databricks Data Connector'
description: 'Databricks Data Connector Documentation'
pagination_prev: null
pagination_next: null
sidebar_position: 5
tags:
  - data-connectors
  - databricks
image: /img/og/og-databricks.png
---
```

**Cloud Format (GitBook):**
```yaml
---
description: 'Databricks Data Connector Documentation'
---

# Databricks Data Connector
```

**Transformation:**
1. Keep only `description` in frontmatter
2. Remove `title`, `sidebar_label`, `tags`, `sidebar_position`, `pagination_prev`, `pagination_next`, `image`
3. Add `# Title` as the first line after frontmatter (use the OSS `title` value)

### Admonitions

Convert Docusaurus admonition syntax to GitBook hint syntax:

| OSS (Docusaurus) | Cloud (GitBook) |
|------------------|-----------------|
| `:::warning[Note]`<br>`content`<br>`:::` | `{% hint style="warning" %}`<br>`content`<br>`{% endhint %}` |
| `:::note`<br>`content`<br>`:::` | `{% hint style="info" %}`<br>`content`<br>`{% endhint %}` |
| `:::tip`<br>`content`<br>`:::` | `{% hint style="success" %}`<br>`content`<br>`{% endhint %}` |
| `:::info`<br>`content`<br>`:::` | `{% hint style="info" %}`<br>`content`<br>`{% endhint %}` |
| `:::caution`<br>`content`<br>`:::` | `{% hint style="warning" %}`<br>`content`<br>`{% endhint %}` |
| `:::danger`<br>`content`<br>`:::` | `{% hint style="danger" %}`<br>`content`<br>`{% endhint %}` |

### Links

Convert absolute OSS links to root-relative paths for GitBook:

| OSS Link Pattern | Cloud Link Pattern |
|------------------|-------------------|
| `/docs/reference/...` | `/reference/...` |
| `/docs/features/...` | `/features/...` |
| `/docs/api/...` | `/api/...` |
| `/docs/components/data-connectors/...` | `/building-blocks/data-connectors/...` |
| `/docs/components/catalogs/...` | `/building-blocks/catalogs/...` |
| `/docs/components/data-accelerators/...` | `/building-blocks/data-accelerators/...` |
| `/docs/components/embeddings/...` | `/building-blocks/embeddings/...` |
| `/docs/components/models/...` | `/building-blocks/model-providers/...` |
| `/docs/components/vectors/...` | `/building-blocks/vectors/...` |
| `/docs/components/views/...` | `/building-blocks/views/...` |
| `/docs/components/secret-stores/...` | `/building-blocks/secret-stores/...` |

**Important:** 
- Do NOT use `../../docs/...` relative paths. Use root-relative paths starting with `/`.
- Ensure `.md` extensions are included in links.
- Internal cross-references within `building-blocks/` can use relative paths like `../data-accelerators/index.md`.

### Imports and Components

Remove Docusaurus-specific imports and components:

```javascript
// Remove these lines entirely
import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';
import DocCardList from '@theme/DocCardList';
```

Remove `<DocCardList />` component calls.

### Cookbook and Secrets Sections

Evaluate cookbook and secrets sections for relevance:
- Remove OSS-specific cookbook links that reference GitHub paths like `/cookbook/...`
- Keep or adapt secrets configuration examples if they apply to cloud

### Special File Mappings

| OSS File | Cloud File |
|----------|------------|
| `index.md` | `README.md` (for directory index) or `index.md` |
| `postgres/index.md` | `postgres.md` or `postgres/index.md` |

## Link Validation

After transformation, validate all links:

1. **Internal links:** Verify target files exist in the cloud repository
2. **Cross-references:** Ensure paths match cloud directory structure
3. **External links:** Keep unchanged (these point to external documentation)

### Common Link Patterns to Validate

```markdown
# These should be root-relative in cloud docs
[secret replacement syntax](/building-blocks/secret-stores/index.md)
[data accelerator](/building-blocks/data-accelerators/index.md)
[reference specification](/reference/spicepod/datasets.md)
[API documentation](/api/HTTP/post-chat-completions.md)
```

## Content Guidelines

- Keep all configuration tables, parameters, and examples
- Preserve code blocks and YAML examples exactly
- Maintain the same heading structure
- Remove OSS-specific content that does not apply to cloud deployment

## Verification Checklist

After syncing, verify:

- [ ] Frontmatter contains only `description` (and optionally `icon`)
- [ ] Title appears as `# Title` after frontmatter
- [ ] All `:::` admonitions converted to `{% hint %}` blocks
- [ ] No `/docs/...` links remain (should be `/...` root paths)
- [ ] No `../../docs/...` relative paths exist
- [ ] No Docusaurus imports remain
- [ ] All internal links resolve to existing files
- [ ] Code blocks and examples are preserved correctly
