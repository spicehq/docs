---
icon: building
description: Spice.ai Enterprise documentation — self-hosted, production-grade deployment of the Spice runtime.
---

# Spice.ai Enterprise

**Spice.ai Enterprise** is a self-hosted, production-grade distribution of the [Spice.ai](https://spice.ai) runtime for organizations that need enterprise security, high availability, and dedicated support.

Enterprise extends the open-source Spice runtime with:

- **Multi-node clustering and HA** — Multi-active distributed query with automatic failover, built on Apache Ballista.
- **Distributed accelerations** — Partition-sharded accelerated datasets across executors with write-through semantics. See [Distributed Accelerations](features/distributed-accelerations.md).
- **Kubernetes Operator** — Automated deployment, scaling, and lifecycle management via `SpicepodSet` and `SpicepodCluster` CRDs.
- **Acceleration snapshots** — Object-store-backed snapshot, bootstrap, and recovery for accelerated datasets. See [Acceleration Snapshots](features/acceleration-snapshots.md).
- **Authorization policy** — Cedar-based fine-grained access control over datasets, models, tools, and endpoints.
- **Enterprise distributions** — NAS (SMB/NFS), CUDA GPU-accelerated, data-only, and ODBC connector builds.
- **Authentication** — OIDC bearer tokens, API keys, and combined auth with identity SQL functions.
- **mTLS cluster security** — Auto-provisioned certificates for secure inter-node communication.
- **SOC 2 Type II compliance** — Code and security audited.
- **Tiered security updates** — Up to 3 years of guaranteed patches.
- **24/7 premium support** — Dedicated account team via Slack Connect, email, and on-call pager.
- **99.9%+ SLA** — Enterprise uptime guarantee with on-call support.

## Deployment Options

| Option                | Description                                                    |
| --------------------- | -------------------------------------------------------------- |
| **Kubernetes (Helm)** | Deploy using the Spice.ai Kubernetes Operator with Helm charts |
| **Docker**            | Enterprise container images from the AWS Marketplace ECR       |
| **AWS Marketplace**   | BYOL and Plan-based licensing via AWS Marketplace              |
| **AWS AMI**           | Pre-built Amazon Machine Images for EC2 deployments            |

## Enterprise vs Open Source vs Cloud

|                      | Open Source                  | Cloud                        | Enterprise                       |
| -------------------- | ---------------------------- | ---------------------------- | -------------------------------- |
| **Pricing**          | Free (Apache 2.0)            | Subscription + usage-based   | Enterprise licensing             |
| **Hosting**          | Self-hosted                  | Fully managed                | Self-hosted BYOL                 |
| **Scale**            | Single-node                  | Multi-node, clustering, HA   | Multi-node, clustering, HA       |
| **Control Plane**    | Static, YAML-driven          | Dynamic, remote (Portal/API) | Dynamic, remote/local (API/YAML) |
| **Distributions**    | Default + Metal              | All                          | All (incl. NAS, ODBC)            |
| **Security Updates** | Latest minus-one (6-8 weeks) | Rolling, automated           | Tiered, up to 3 years            |
| **SLA**              | N/A                          | 99.9%                        | 99.9%+                           |
| **Support**          | Community                    | Standard (email, Slack)      | Premium 24/7 on-call             |
| **Compliance**       | N/A                          | SOC 2 Type II                | SOC 2 Type II                    |

{% hint style="warning" %}
Spice.ai Enterprise requires a **commercial license** or an **AWS Marketplace subscription**. [Contact us](https://spice.ai/contact) to get started or request a trial.
{% endhint %}

## Production-Ready by Design

Spice.ai Enterprise is the supported path for running Spice in production. The [**Production Readiness**](production/README.md) section is the canonical reference for promoting a deployment to production:

- [**Production Readiness Checklist**](production/README.md) \u2014 sign-off list covering topology, storage, observability, security, and upgrades.
- [**High Availability**](production/high-availability.md) \u2014 multi-replica, multi-AZ, and `SpicepodCluster` topologies.
- [**Storage**](production/storage.md) \u2014 NVMe, `io2` Block Express, Premium SSD v2, and acceleration sizing.
- [**Observability**](production/observability.md) \u2014 Prometheus metrics, Grafana dashboard, log routing, and the recommended alert set.
- [**Security**](production/security.md) \u2014 image pinning, Pod Security Standards, NetworkPolicy, mTLS, and secrets management.
- [**Upgrades**](production/upgrades.md) \u2014 versioning, rolling upgrades, and rollback procedures.

## Get Started

<table data-card-size="large" data-view="cards">
<thead><tr><th></th><th></th></tr></thead>
<tbody>
<tr><td><strong>Quickstart</strong></td><td>Deploy Spice.ai Enterprise on Kubernetes in minutes.</td></tr>
<tr><td><strong>Distributions</strong></td><td>Choose the right runtime distribution for your workload.</td></tr>
<tr><td><strong>Kubernetes Operator</strong></td><td>Manage Spicepods with the Spice K8s Operator.</td></tr>
<tr><td><strong>Authentication</strong></td><td>Configure OIDC, API keys, and identity functions.</td></tr>
</tbody>
</table>
