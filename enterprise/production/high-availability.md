---
description: Multi-replica, multi-AZ topology for production Spice.ai Enterprise deployments.
icon: arrows-split-up-and-left
---

# High Availability

Production Spice.ai Enterprise deployments are built on three HA primitives: **multiple replicas**, **multi-zone scheduling**, and **multi-active distributed query**. This page documents the supported topologies and how to configure them.

## Topology decision tree

```
                  ┌──────────────────────────────────────┐
                  │ Does the workload need shared        │
                  │ acceleration or distributed query?   │
                  └────────────────────┬─────────────────┘
                                       │
                ┌──────────────────────┴──────────────────────┐
                │                                             │
              No│                                             │Yes
                ▼                                             ▼
    ┌────────────────────────┐                 ┌───────────────────────────┐
    │  SpicepodSet           │                 │   SpicepodCluster         │
    │  replicas >= 2         │                 │   2+ schedulers           │
    │  Stateless, behind LB  │                 │   3+ executors            │
    └────────────────────────┘                 └───────────────────────────┘
```

| Topology                    | When to use                                                                                       | HA mechanism                                                       |
| --------------------------- | ------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------ |
| `SpicepodSet` `replicas: 1` | Edge / sidecar deployments where availability is bounded by the parent service.                   | Pod restart on failure; `PodDisruptionBudget` is not applicable.    |
| `SpicepodSet` `replicas >= 2` (stateless) | Stateless query routing or in-memory accelerations.                                | Multiple replicas behind a load balancer with health-checked routing. |
| `SpicepodSet` `replicas >= 2` (stateful)  | File-based accelerations that benefit from local replicas.                         | Per-replica `StatefulSet`s with PVCs; ordered or parallel rollout.  |
| `SpicepodCluster`           | Distributed analytical query, shared accelerations, multi-tenant deployments.                     | Multi-active schedulers + executors over object-store-backed state. |

## Multi-zone scheduling

Spread replicas across availability zones so a single-AZ outage cannot take the deployment down.

### `topologySpreadConstraints`

```yaml
apiVersion: spice.ai/v1
kind: SpicepodSet
metadata:
  name: prod-router
spec:
  replicas: 3
  topologySpreadConstraints:
    - maxSkew: 1
      topologyKey: topology.kubernetes.io/zone
      whenUnsatisfiable: ScheduleAnyway
      labelSelector:
        matchLabels:
          spice.ai/app: prod-router
  spicepod: |
    name: prod-router
    kind: Spicepod
    version: v1
```

For `SpicepodCluster`, configure the spread separately on `schedulerSetSpec` and `executorSetSpec` so each tier is independently AZ-balanced.

### Pod anti-affinity

For workloads that should never co-locate on a single node \u2014 for example, scheduler replicas \u2014 use pod anti-affinity:

```yaml
spec:
  schedulerSetSpec:
    replicas: 2
    podAntiAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        - labelSelector:
            matchLabels:
              spice.ai/cluster-role: scheduler
          topologyKey: kubernetes.io/hostname
```

## PodDisruptionBudget

Always pair a multi-replica `SpicepodSet` or `SpicepodCluster` with a `PodDisruptionBudget` so cluster maintenance (node upgrades, autoscaler scale-downs) cannot drain the workload to zero:

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: spiceai-pdb
spec:
  minAvailable: 2
  selector:
    matchLabels:
      spice.ai/app: prod-router
```

For `SpicepodCluster`, create one `PodDisruptionBudget` per role (scheduler and executor).

## Update strategies

Production deployments use rolling updates so the workload remains available during a chart, image, or Spicepod change. The operator's [`update_strategy`](../kubernetes/spicepodset.md) controls rollout behavior:

| Strategy           | Behavior                                                                                                                                  | When to use                                                       |
| ------------------ | ----------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------- |
| `RollingParallel`  | (default) Update unhealthy replicas first, then proceed in parallel batches bounded by `max_unavailable`. Pod identity is not preserved.  | Stateless deployments that can tolerate temporary capacity loss.   |
| `RollingOrdered`   | Update one replica at a time in order. Preserves per-replica identity for stateful workloads.                                              | Stateful accelerations and `SpicepodCluster` schedulers.           |
| `Parallel`         | Update all replicas simultaneously. Causes a brief outage.                                                                                | Non-production / dev environments only.                            |

```yaml
spec:
  replicas: 5
  update_strategy:
    type: RollingParallel
    max_unavailable: 1
```

## Load balancing and ingress

Spice.ai exposes three ports (`8090` HTTP, `50051` Arrow Flight, `9090` Prometheus). For production:

- Front the HTTP API with an L4 or L7 load balancer that performs health checks against `GET /health` on `8090`.
- For Arrow Flight, prefer an L4 NLB to avoid HTTP/2 framing overhead in L7 load balancers.
- The Prometheus port should never be exposed externally \u2014 leave it on the in-cluster `ClusterIP` `Service`.

For sticky-session connection pooling (clients that benefit from cached query plans), enable session affinity on the `Service`:

```yaml
service:
  type: ClusterIP
  sessionAffinity: ClientIP
  sessionAffinityConfig:
    clientIP:
      timeoutSeconds: 600
```

## Multi-active distributed query

`SpicepodCluster` runs multiple schedulers in multi-active mode with shared state in an object store. Failover is automatic: executors re-register with surviving schedulers via the cluster's known-scheduler list, and in-flight stages are retried by the surviving schedulers without restarting the query.

See the [Distributed Query](../features/distributed-query.md) feature documentation for the underlying mechanics.

## Capacity planning

| Component                | Sizing baseline                                                                              | Scaling signal                                       |
| ------------------------ | -------------------------------------------------------------------------------------------- | ---------------------------------------------------- |
| `SpicepodSet` (stateless)| Start at `cpu: 2 / memory: 4Gi` per replica; one replica per ~50 concurrent queries.         | p95 query latency; `runtime.task_history` queue depth. |
| `SpicepodSet` (stateful) | Memory \u2265 working set of the largest hot acceleration.                                   | Acceleration cache hit rate; PVC IOPS saturation.    |
| `SpicepodCluster` scheduler | `cpu: 1 / memory: 2Gi` per replica; one replica per ~100 active query plans.              | Query plan latency; scheduler CPU.                   |
| `SpicepodCluster` executor  | `cpu: 4 / memory: 16Gi` per replica; size shuffle disk to ~25% of dataset volume.         | Stage queue depth; spill-to-disk volume.             |

Validate against actual workload using the [Grafana dashboard](observability.md#grafana-dashboard) before sizing for steady state.
