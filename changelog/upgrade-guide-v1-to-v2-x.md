---
description: Breaking changes and migration steps when upgrading the Spice runtime from v1.x to v2.x.
icon: arrow-up-right-dots
---

# Upgrade Guide from v1.x to v2.x

Most v1 spicepods continue to work on v2.0 — `v1` remains supported and deprecated fields auto-migrate at load time — so many deployments can upgrade by updating the image alone. The steps below cover the breaking changes that may require manual action. Review each before upgrading a production deployment. For the full v2.0 changelog, see the [OSS release notes](https://spiceai.org/releases/v2.0-stable).

### 1. Adopt Spicepod `v2` (recommended)

`spice init` now creates `version: v2` spicepods. `v1` spicepods remain supported with automatic migration, but `v1beta1` is no longer accepted. To move to `v2`, set `version: v2` and update the following fields — each auto-migrates from `v1`, but updating now clears the deprecation:

| v1 (deprecated)               | v2 (preferred)                                                |
| ----------------------------- | ------------------------------------------------------------- |
| `runtime.results_cache`       | `runtime.caching.sql_results` (`cache_max_size` → `max_size`) |
| `runtime.memory_limit`        | `runtime.query.memory_limit`                                  |
| `runtime.temp_directory`      | `runtime.query.temp_directory`                                |
| `dataset.invalid_type_action` | `dataset.unsupported_type_action`                             |

### 2. Update changed configuration

- **DuckDB parameter rename:** `partitioned_write_flush_threshold` → `partitioned_write_flush_threshold_rows`.
- **Default query memory limit raised from 70% to 90%.** If you relied on the previous default to leave headroom for other processes on the host, set it explicitly via `runtime.query.memory_limit`.

### 3. Update queries and API clients

- **S3 metadata columns renamed:** `location`, `last_modified`, `size` → `_location`, `_last_modified`, `_size`. Update any queries that reference these columns.
- **`/v1/search` always returns an array** in `matches`, even for a single result. Update clients that assumed a scalar value.
- **`/v1/evals` API removed.** Remove integrations that depend on it.

### 4. Update model providers

- **Perplexity model provider removed.** Re-point affected models to another provider.
- **x.ai models use the `/v1/responses` endpoint exclusively.** Ensure x.ai integrations target the Responses API.

### 5. Update observability

- **Metric renames:** `accelerated_refresh` → `acceleration_refresh`, and the `last_refresh_time` gauge is renamed to include the milliseconds unit. Update dashboards and alerts that reference these metric names.
