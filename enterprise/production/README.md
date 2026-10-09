---
description: Production readiness checklist for deploying Spice.ai Enterprise.
icon: shield-check
---

# Production Readiness

This section is the canonical reference for running Spice.ai Enterprise in production. It covers the architectural, operational, and security decisions required to operate Spice at scale with the SLAs expected from an enterprise data and AI platform.

Use this checklist as the definitive sign-off list before promoting a Spice.ai Enterprise deployment to production.

## At a Glance

| Area                                     | Production Target                                                           | Reference                                            |
| ---------------------------------------- | --------------------------------------------------------------------------- | ---------------------------------------------------- |
| **Topology**                             | Multi-replica, multi-AZ; `SpicepodCluster` for distributed query workloads. | [High Availability](high-availability.md)            |
| **Storage**                              | Local NVMe for accelerations; `io2` / Premium SSD v2 for shared state.      | [Storage](storage.md)                                |
| **Observability**                        | Prometheus scraping, Grafana dashboard, log aggregation, alerts.            | [Observability](observability.md)                    |
| **Security**                             | mTLS between nodes, OIDC authentication, NetworkPolicy, image pinning.     | [Security](security.md)                              |
| **Authentication**                       | OIDC bearer tokens or API keys; identity SQL functions.                     | [Authentication](../features/authentication.md)      |
| **Upgrades**                             | Tiered security updates; rolling, validated upgrades.                       | [Upgrades](upgrades.md)                              |
| **Backup / DR**                          | Object-store-backed cluster state; Spicepod source of truth in Git.         | [Storage \u2192 Disaster recovery](storage.md#disaster-recovery) |
| **Support**                              | 24/7 premium support; 99.9%+ SLA.                                           | [Spice.ai Enterprise](../README.md)                  |

## Production Readiness Checklist

### Topology and high availability

- [ ] Run at least **two scheduler replicas** for HA. See [High Availability](high-availability.md).
- [ ] Run at least **three executor replicas** to tolerate single-node failure during shuffles.
- [ ] Use **`SpicepodCluster`** for distributed query workloads; use **`SpicepodSet`** with `replicas >= 2` for stateless query routers.
- [ ] Spread replicas across availability zones with `topologySpreadConstraints` or pod anti-affinity.
- [ ] Configure a `PodDisruptionBudget` covering scheduler and executor pools.
- [ ] Front the runtime with a load balancer that performs L4 health checks against `/health` (port `8090`).

### Storage and acceleration

- [ ] Local NVMe-backed nodes (AWS `i4i` / `m6id` / `c7gd` / `r7gd`, Azure `Lsv3` / `Ddsv5`, GCP `*-lssd`) for [accelerations](https://spiceai.org/docs/components/data-accelerators).
- [ ] Backing PVC class is `io2` Block Express (AWS) or Premium SSD v2 (Azure) when shared / replica-attachable persistence is required.
- [ ] S3-compatible object store provisioned for `SpicepodCluster` shared state; bucket versioning and lifecycle policies are enabled.
- [ ] Object store backing `runtime.scheduler.state_location` supports conditional writes (ETag / `If-Match`) — required for multi-active correctness.
- [ ] Every accelerated dataset and view targeted at `SpicepodCluster` declares `acceleration.partition_by`. See [Distributed Query → Partitioning](../features/distributed-query.md#partitioning-sharding-and-partition-aware-queries).
- [ ] Acceleration sizing has been validated against expected dataset growth for the next 12 months. See [Storage](storage.md).

### Observability

- [ ] Prometheus scrapes the `9090` metrics endpoint via the Spice Helm chart `PodMonitor` or the operator `ServiceMonitor`.
- [ ] [Grafana dashboard](observability.md#grafana-dashboard) is imported and connected to the Prometheus data source.
- [ ] Logs are forwarded to a centralized aggregator (CloudWatch, Loki, Datadog, Elastic).
- [ ] [Alerts](observability.md#alerts) are configured for query error rate, refresh failures, executor crashloops, and certificate expiry.
- [ ] OpenTelemetry traces are exported when distributed tracing is in use.

### Security

- [ ] [Authentication](../features/authentication.md) is enabled (OIDC or API keys); no unauthenticated endpoints are exposed externally.
- [ ] [mTLS](../features/mtls.md) is enabled for all `SpicepodCluster` deployments (default; `allowInsecureConnections` must remain unset).
- [ ] Container images are pinned to immutable digests (`sha256:...`), not floating tags.
- [ ] `NetworkPolicy` restricts ingress to load balancer / ingress controller IPs and egress to required upstream endpoints. See [Security](security.md#network-policy).
- [ ] Pod runs as the default UID `65534` with `runAsNonRoot: true` and a `readOnlyRootFilesystem`.
- [ ] Secrets are sourced from a [secret store](https://spiceai.org/docs/components/secret-stores) (AWS Secrets Manager, Azure Key Vault, Kubernetes Secret) rather than embedded in `values.yaml`.
- [ ] Operator and runtime images are scanned by the organization's image scanner (Trivy, Snyk, Aqua) before promotion.

### Capacity and resource limits

- [ ] CPU and memory `requests`, and a memory `limit`, are set on every pod. Set `resources.limits.cpu` only where a pod should be prevented from bursting above its request.
- [ ] The CPU core count has been reviewed, and either the default is appropriate (all cores available on the machine) or it has been set via `runtime.cpu.cores` in the Spicepod or the `SPICE_CPU_CORES` environment variable. Setting it above the CPU request allows bursting. See [Request CPU and memory](../kubernetes/user-guide.md#request-cpu-and-memory).
- [ ] Resource sizing has been validated under projected concurrent query load. Start at `cpu: 2 / memory: 8Gi` and scale based on `runtime.task_history` and Prometheus metrics.
- [ ] [Crashloop protection](../kubernetes/spicepodset.md#crashloop-protection) is enabled. The operator chart ships `pauseCrashloopingPodsThreshold: 0`, which disables it — set a positive integer (the binary's own default is `10`).

### Lifecycle and upgrades

- [ ] [Upgrades](upgrades.md) are validated in a non-production environment before promoting to prod.
- [ ] `RollingParallel` or `RollingOrdered` update strategy is configured on every `SpicepodSet`.
- [ ] Spicepod manifests are stored in Git and applied via Argo CD, Flux, or the equivalent GitOps controller.
- [ ] A documented rollback procedure exists, including the ability to roll back the operator chart and the runtime image independently.

### Compliance and support

- [ ] An active **Spice.ai Enterprise license** or AWS Marketplace subscription is attached to the deployment.
- [ ] Tenant or workload-level audit logs are forwarded to the organization's SIEM.
- [ ] On-call rotation includes the Spice.ai Enterprise [premium support](../README.md) contact path.
- [ ] SLA targets (99.9%+) are documented in the team's runbook with monitoring tied back to the alert system.

## When to use what

| Workload pattern                                       | Recommended deployment                                             |
| ------------------------------------------------------ | ------------------------------------------------------------------ |
| Single-tenant edge / sidecar accelerator               | `SpicepodSet` with `replicas: 1`, local NVMe.                      |
| Multi-replica stateless query API                      | `SpicepodSet` with `replicas >= 2`, behind an internal LB.         |
| File-based acceleration with persistence (DuckDB, etc.) | `SpicepodSet` with `volume.storage_requests` on `io2` / Premium SSD v2. |
| Distributed query, multi-AZ, multi-tenant              | `SpicepodCluster` with 2+ schedulers and 3+ executors.             |
| Air-gapped / regulated environment                     | Self-hosted with private registry; pinned digests; mTLS enforced.  |

## Next steps

1. Walk through the [checklist above](#production-readiness-checklist) end to end.
2. Read the area-specific guides linked from the [At a Glance](#at-a-glance) table.
3. Engage [Spice.ai Enterprise support](../README.md) for an architecture review prior to go-live.
