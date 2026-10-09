---
description: Automatic mTLS for secure inter-node communication in distributed Spice.ai clusters.
icon: shield-halved
---

# mTLS Cluster Security

Spice.ai Enterprise secures inter-node communication in distributed query clusters using mutual TLS (mTLS). When using the [SpicepodCluster](../kubernetes/spicepodcluster.md) CRD, certificates are provisioned and managed automatically.

## Port Separation

| Port  | Visibility   | Services                        | mTLS Required |
| ----- | ------------ | ------------------------------- | ------------- |
| 50051 | Public       | Arrow Flight, OpenTelemetry     | Optional      |
| 8090  | Public       | HTTP API                        | Optional      |
| 9090  | Public       | Prometheus metrics              | No            |
| 50052 | **Internal** | Scheduler gRPC, Cluster Service | **Required**  |

Public-facing ports (50051, 8090) serve client traffic and can optionally use TLS. The internal port (50052) carries cluster coordination traffic and **requires mTLS** in production.

## Automatic Certificate Management (Kubernetes)

When deployed via `SpicepodCluster`, the operator handles the full certificate lifecycle:

1. **Root CA** — Auto-generated self-signed CA stored in a Kubernetes Secret.
2. **Leaf certificates** — Per-node certificates with appropriate Subject Alternative Names (SANs).
3. **Expiry tracking** — Certificate expiry is surfaced via Prometheus metrics.

No manual PKI setup is required.

## Manual Certificate Management (CLI)

For non-Kubernetes deployments, use the Spice CLI to manage a PKI:

### Initialize a PKI

```bash
spice cluster tls init
```

### Generate Node Certificates

```bash
spice cluster tls add scheduler1
spice cluster tls add executor1 --host executor1.cluster.local
```

### Start Nodes with mTLS

```bash
spiced --role scheduler \
  --node-mtls-ca-certificate-file ca.pem \
  --node-mtls-certificate-file scheduler1.pem \
  --node-mtls-key-file scheduler1-key.pem \
  --node-advertise-address scheduler1.cluster.local

spiced --role executor \
  --node-mtls-ca-certificate-file ca.pem \
  --node-mtls-certificate-file executor1.pem \
  --node-mtls-key-file executor1-key.pem \
  --scheduler-address https://scheduler1.cluster.local:50052 \
  --node-advertise-address executor1.cluster.local
```

## CLI Reference

| Flag                              | Description                           |
| --------------------------------- | ------------------------------------- |
| `--role`                          | Node role: `scheduler` or `executor`  |
| `--node-mtls-ca-certificate-file` | Path to the CA certificate            |
| `--node-mtls-certificate-file`    | Path to the node certificate          |
| `--node-mtls-key-file`            | Path to the node private key          |
| `--node-advertise-address`        | Hostname for inter-node communication |
| `--node-bind-address`             | Bind address for the internal port    |
| `--scheduler-address`             | Scheduler URL (executor only)         |
| `--allow-insecure-connections`    | Disable mTLS (dev/test only)          |

## Internal Cluster Services (Port 50052)

The internal gRPC services secured by mTLS. See [Distributed Query → Internal gRPC](distributed-query.md#internal-grpc-port-50052) for the full surface.

| RPC                             | Description                                                                                              |
| ------------------------------- | -------------------------------------------------------------------------------------------------------- |
| `GetAppDefinition`              | Executors fetch the full Spicepod definition (datasets, catalogs, views, UDFs) from the scheduler.       |
| `ExpandSecret`                  | Executors request secret values from the scheduler's secret store.                                       |
| `GetSchedulers`                 | Executors fetch the live scheduler membership list to open a poll loop to every scheduler.              |
| `AllocateInitialPartitions`     | Executors fetch their assigned partition filter expressions per accelerated table.                       |
| `ControlStream` (bidirectional) | Carries executor heartbeats and metrics; receives partition update, refresh, and cancel commands.        |
| `GetTaskHistory`                | Federated `runtime.task_history` fan-out across peer schedulers.                                         |
| `GetMetrics`                    | On-demand OTLP metrics collection across the cluster.                                                    |

{% hint style="danger" %}
The `--allow-insecure-connections` flag disables all mTLS verification. Never use this in production — all inter-node communication will be unencrypted and unauthenticated.
{% endhint %}
