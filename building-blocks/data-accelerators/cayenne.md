---
description: 'Cayenne Data Accelerator (Vortex) Documentation'
---

# Cayenne Data Accelerator

{% hint style="info" %}
**Alpha:** The Cayenne Data Accelerator is in Alpha. Features and configuration may change. Available in Spice v1.9.0-rc.1 and later.
{% endhint %}

Cayenne is a Spice data acceleration engine designed for high-performance, scalable query on large-scale datasets. Built on [Vortex](https://github.com/vortex-data/vortex), a next-generation columnar file format, Cayenne combines columnar storage with in-process metadata management to provide fast query performance to scale to datasets beyond 1TB.

## Why Vortex?

Cayenne uses Vortex as its storage format, providing significant performance advantages:

- **100x faster random access reads** compared to modern Apache Parquet
- **10-20x faster scans** for analytical queries
- **5x faster writes** with similar compression ratios
- **Zero-copy compatibility** with Apache Arrow for efficient data processing
- **Extensible architecture** with pluggable encoding, compression, and layout strategies

Vortex is a Linux Foundation (LF AI & Data) project under Apache-2.0 license with neutral governance.

While [DuckDB](duckdb.md) excels for datasets up to approximately 1TB, Cayenne with Vortex is designed to scale beyond these limits.

For detailed Vortex performance benchmarks, visit [bench.vortex.dev](https://bench.vortex.dev).

## Configuration

To use Cayenne as the data accelerator, specify `cayenne` as the `engine` for acceleration. Cayenne only supports `mode: file` and stores data on disk.

```yaml
datasets:
  - from: spice.ai:path.to.my_dataset
    name: my_dataset
    acceleration:
      engine: cayenne
      mode: file
```

#### `params`

| Parameter name                 | Description     |
| ------------------------------ | ------ |
| `cayenne_compression_strategy` | Determines the type of compression to use when accelerating datasets. Defaults to [`btrblocks`](https://www.cs.cit.tum.de/fileadmin/w00cfj/dis/papers/btrblocks.pdf). Supports `btrblocks` or [`zstd`](https://github.com/facebook/zstd). |
| `cayenne_unsupported_type_action` | Determines what action to take when a data type that is not supported is encountered. See [`unsupported_type_action` for more information](../../reference/spicepod/datasets.md#unsupported_type_action). |
| `cayenne_footer_cache_mb` | Size of the in-memory Vortex footer cache in megabytes. Larger values improve query performance for repeated scans. Defaults to `128MiB`. |
| `cayenne_segment_cache_mb` | Size of the in-memory Vortex segment cache in megabytes, to cache decompressed data segments for improved query performance in repeated scans. Defaults to `256MiB`. |

## Features

### High-Performance Columnar Storage

Cayenne uses Vortex's advanced columnar format, which provides:

- **Efficient Compression**: Cascading compression with nested encoding schemes including RLE, dictionary encoding, FastLanes, FSST, and ALP
- **Rich Statistics**: Lazy-loaded summary statistics for query optimization
- **Extensible Encodings**: Pluggable physical layouts optimized for different data patterns
- **Wide Table Support**: Efficient handling of tables with many columns through zero-copy metadata access

### Checking index use <a href="#checking-index-use" id="checking-index-use"></a>

An `indexes` entry is a point-lookup index. The scan uses it when the query pins every column of that entry to a single literal with `=`. `EXPLAIN` and `EXPLAIN ANALYZE` both print the decision on `CayenneAccelerationExec`:

```text
CayenneAccelerationExec: snapshots_scanned=1, files_scanned=4, lookup_index=id, lookup_index_outcome=selected, candidate_files=1, candidate_rows=1
```

| `lookup_index_outcome` | Meaning                                                                                          |
| ---------------------- | ------------------------------------------------------------------------------------------------ |
| `selected`             | The lookup restricted the scan to the matching rows. `candidate_files` and `candidate_rows` say how much. |
| `empty`                | The key is absent, so the scan reads no file.                                                    |
| `unbuilt`              | No index covers the rows this lookup reads. Definitions apply the next time the dataset loads.  |
| `snapshot_mismatch`    | The files being scanned are not the files the index was built from.                             |
| `not_applicable`       | The predicates do not pin every column of one index to one literal, so the scan reads the table. |

`WHERE id = 1` against an index on `id` is `selected` when the key exists, and `empty` when it does not. `WHERE a = 1 AND b = 2` against an index on `(a, b)` is the same kind of lookup. An `IN` list, including several keys against a composite index, is `not_applicable`. A range, an `OR`, a cast of the indexed column, and a composite index with any column left unconstrained are `not_applicable` as well. `lookup_index=none` means no index shape matched.

Float columns cannot be an index key. Use an integer, decimal, string, or another type with exact equality.

```sql
EXPLAIN SELECT * FROM analytics_data WHERE id = 42;
```

## Limitations

Consider the following limitations when using Cayenne acceleration:

- **Alpha Status**: Cayenne is in active development. Configuration options may change between releases.
- **File Mode Only**: Cayenne only supports `mode: file` and does not support in-memory (`mode: memory`) acceleration.
- **`on_conflict` across batches**: Cayenne applies [`on_conflict`](../../features/data-acceleration/README.md#duplicate-keys-in-one-cayenne-refresh) inside each incoming batch. `upsert` keeps the last row for a primary key in that batch. The same primary key in two batches of one refresh fails with `Incoming data contains duplicate primary key across batches`. A cold load or an overlapping append re-read can produce that split. Append without a primary key and read the latest row from a view when a refresh can contain a key twice.
- **Data Cleanup Requires `retention_sql`**: Data deletion and cleanup operations require configuring [`retention_sql`](../../reference/spicepod/datasets.md#accelerationretention_sql) to define retention policies. Manual `DELETE` statements can also be executed directly.
- **No Snapshot Support**: Cayenne does not yet support acceleration snapshots in Spice.ai OSS. Snapshots are available in [Spice.ai Enterprise](../../enterprise/features/acceleration-snapshots.md).
- **Data Types**: Some advanced data types may have limited support. Test your specific schema requirements.
- **Index shape**: Indexes serve equality lookups that pin every indexed column to one literal. Confirm the outcome with [`EXPLAIN`](#checking-index-use). An `IN` list, a range, an `OR`, or a cast of the indexed column reads the table.

{% hint style="warning" %}
**Alpha Software:** As an Alpha feature, Cayenne should be thoroughly tested in development environments before production deployment. Monitor release notes for updates, breaking changes, and new capabilities.
{% endhint %}

## Resource Considerations

Resource requirements for Cayenne depend on dataset size, query patterns, and metastore configuration.

### Memory

Cayenne manages memory efficiently through columnar storage and selective caching. Allocate sufficient memory based on:

- Dataset size and schema complexity
- Query concurrency requirements
- Caching configuration

### Storage

Cayenne stores data in a columnar format optimized for analytical queries. Ensure adequate disk space for:

- Acceleration storage
- Temporary files during query execution
- Metadata and catalog information

## Example Spicepod

Complete example configuration using Cayenne:

```yaml
version: v1
kind: Spicepod
name: cayenne-example

datasets:
  - from: s3://my-bucket/data/
    name: analytics_data
    params:
      file_format: parquet
    acceleration:
      engine: cayenne
      enabled: true
      refresh_mode: full
      refresh_check_interval: 1h
```
