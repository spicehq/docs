---
description: Spice.ai Enterprise runtime distributions for different workload requirements.
icon: cube
---

# Distributions

Spice.ai Enterprise provides multiple runtime distributions optimized for different workloads. All distributions are available in Enterprise; some are restricted or nightly-only in open source.

{% hint style="info" %}
The Spice runtime is **64-bit only**.
{% endhint %}

## Supported Platforms

| Platform | Architecture            | Minimum CPU Features                     |
| -------- | ----------------------- | ---------------------------------------- |
| Linux    | x86_64                  | AVX2, FMA, BMI1/2, LZCNT, POPCNT         |
| Linux    | aarch64 (arm64)         | NEON, FP16 (FEAT\_FP16), FHM (FEAT\_FHM) |
| macOS    | aarch64 (Apple Silicon) | Native                                   |

## Distribution Availability

| Distribution / Capability     | Open Source      | Spice Cloud | Enterprise |
| ----------------------------- | ---------------- | ----------- | ---------- |
| Default (Data + AI)           | ✅                | ✅           | ✅          |
| Data-only                     | Nightly only     | ✅           | ✅          |
| NAS (SMB + NFS)               | Nightly only     | —           | ✅          |
| Metal (macOS)                 | ✅                | ✅           | ✅          |
| CUDA (Linux)                  | Nightly only     | ✅           | ✅          |
| Allocator variants            | Nightly only     | ✅           | ✅          |
| ODBC connector                | Local build only | ✅           | ✅          |
| HTTP user-defined functions   | Local build only | ✅           | ✅          |
| WASM user-defined functions   | Local build only | ✅           | ✅          |

Inline SQL user-defined functions (`from: sql`) are available in every distribution. The HTTP and WebAssembly tiers are shipped pre-built in Cloud and Enterprise distributions; open source users can enable them by building locally with the `http-functions` and `wasm-functions` cargo features. See [User-Defined Functions](../features/functions.md) for the full reference.

## Default Distribution

Includes all standard data connectors, embedded data accelerators (Spice Cayenne, DuckDB, SQLite), AI/ML model inference (LLMs, embeddings), and search capabilities (vector and BM-25 full-text search). It links the system allocator; see [Allocator Variants](#allocator-variants) to run against a different one.

## Data-Only Distribution

Excludes AI/ML model support. Provides a smaller binary size and reduced attack surface for workloads that only need data federation and acceleration.

## NAS Distribution

Adds SMB and NFS data connector support. **Enterprise-only for production use.**

## GPU-Accelerated Distributions

### Metal (macOS)

GPU-accelerated AI/ML inference on Apple Silicon.

### CUDA (Linux)

CUDA GPU-accelerated model inference. Supported compute capabilities:

| Compute Capability | GPUs               |
| ------------------ | ------------------ |
| 80                 | A100, A30          |
| 86                 | RTX 30xx, A40, A10 |
| 87                 | Jetson Orin        |
| 89                 | RTX 40xx, L40, L4  |
| 90                 | H100, H200         |

The AWS Marketplace ECR registry does not carry a CUDA image; it publishes the Default, models, and jemalloc variants (see [Docker](../deployment/docker.md)). [Contact us](https://spice.ai/contact) for a CUDA-enabled Enterprise deployment.

## Allocator Variants

Different memory allocators can significantly impact performance depending on workload characteristics.

The allocator is selected when the runtime is built, and a build links exactly one. The Default distribution enables no allocator feature, so it links the system allocator.

The AWS Marketplace ECR registry publishes the jemalloc variant. [Contact us](https://spice.ai/contact) about an snmalloc or mimalloc build.

### snmalloc

Optimized for concurrent workloads.

### jemalloc

Alternative allocator that may perform better for certain memory allocation patterns. Marketplace images carry the `-jemalloc` suffix and are published from `2.2.1-enterprise` onwards:

```bash
docker pull 709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/spiceai-enterprise-byol:2.2.1-enterprise-jemalloc
```

### mimalloc

Microsoft's mimalloc allocator, designed for performance and security.

### System Allocator

Uses the system's default allocator (glibc malloc on Linux). This is what the Default distribution links, so the `<version>-enterprise` and `<version>-enterprise-models` images use it.

## Choosing a Distribution

| Use Case                                | Recommended Distribution |
| --------------------------------------- | ------------------------ |
| General purpose with AI capabilities    | Default                  |
| Data federation only, minimal footprint | Data-only                |
| Network attached storage (SMB/NFS)      | NAS                      |
| macOS with GPU acceleration             | Metal                    |
| Linux with NVIDIA GPU                   | CUDA                     |
| Memory allocation tuning                | Allocator variants       |
