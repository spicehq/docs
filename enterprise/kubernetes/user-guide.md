---
description: Step-by-step guide to deploying and operating Spice.ai workloads with the Kubernetes Operator.
icon: book-open
---

# User Guide

This guide walks through deploying and operating Spice.ai on Kubernetes with the operator — from installing the controller to configuring storage, networking, rollouts, scaling, and observability. For exhaustive field references, see [SpicepodSet](spicepodset.md) and [SpicepodCluster](spicepodcluster.md).

{% hint style="info" %}
All manifests below use the current `spice.ai/v2` API version. Existing `spice.ai/v1` / `spice.ai/v1alpha1` manifests continue to apply and are converted automatically — see [Migrating from `spice.ai/v1`](spicepodset.md#migrating-from-spiceaiv1).
{% endhint %}

## Prerequisites

- Kubernetes 1.33+
- Helm 3.x
- `kubectl` configured for your cluster
- Access to a Spice runtime image (the enterprise image is pulled from the AWS Marketplace ECR registry; a [pull secret](#use-a-private-registry) is required)

## 1. Install the operator

The operator is distributed as an OCI Helm chart. Subscribe to the [AWS Marketplace](../deployment/aws-marketplace.md) listing, authenticate to the Marketplace ECR registry, then install into its own namespace:

```bash
helm install spiceai-operator \
  oci://709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/charts/spiceai-operator \
  --namespace spiceai-operator-system --create-namespace
```

The chart installs the `SpicepodSet` and `SpicepodCluster` CRDs by default (`crds.enabled: true`). Verify the controller is running:

```bash
kubectl -n spiceai-operator-system get pods
kubectl get crds | grep spice.ai
```

See the [Overview](README.md#helm-values) for the full list of Helm values.

## 2. Deploy your first Spicepod

A `SpicepodSet` is the simplest way to run a Spicepod. Create `spicepodset.yaml`:

```yaml
apiVersion: spice.ai/v2
kind: SpicepodSet
metadata:
  name: my-spicepod
  namespace: default
spec:
  replicas: 1
  spicepod:
    name: my-spicepod
    kind: Spicepod
    version: v1
    # datasets, catalogs, models, views, ... go here
```

Apply it and watch it come up:

```bash
kubectl apply -f spicepodset.yaml
kubectl get spicepodset my-spicepod -w
# NAME          READY   ROLE     AGE   LAST UPDATED
# my-spicepod   1/1     <none>   30s   5s
```

Each `SpicepodSet` is deployed as one or more `StatefulSet`s — one per replica, each with an ordinal suffix — so every pod has a stable identity and predictable DNS name, even with a single replica. The `spicepod` object is stored in a `ConfigMap` and mounted into each pod; editing it triggers a rollout.

Port-forward and query the runtime:

```bash
kubectl port-forward svc/my-spicepod 8080:8080
curl http://localhost:8080/health
```

## 3. Configure the workload

The sections below cover the most common configuration tasks. Each maps to a field on `spec`; see the [SpicepodSet reference](spicepodset.md) for every option.

### Set the container image

```yaml
spec:
  image:
    repository: 709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/spiceai-enterprise-plan
    tag: 2.0.0-enterprise-models
    pullPolicy: IfNotPresent
```

`repository` is the full registry + name path. To use a public build instead, set `repository: spiceai/spiceai` (Docker Hub) or `repository: ghcr.io/spiceai/spiceai`.

#### Use a private registry

Provide credentials with a pull secret:

```bash
kubectl create secret docker-registry spice-pull-secret \
  --docker-server=709825985650.dkr.ecr.us-east-1.amazonaws.com \
  --docker-username=AWS --docker-password="$(aws ecr get-login-password)"
```

```yaml
spec:
  image:
    repository: 709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/spiceai-enterprise-plan
    tag: 2.0.0-enterprise-models
    pullSecret: spice-pull-secret
```

### Configure ports

```yaml
spec:
  http:
    port: 8090
  flight:
    port: 50051
  metrics:
    port: 9090
```

These set what the Spiced container listens on. The managed `Service` always exposes fixed ports `8080` (HTTP), `50051` (Flight), and `9090` (metrics), mapping `targetPort` to the configured Spiced ports above.

### Request CPU and memory

```yaml
spec:
  resources:
    requests:
      cpu: 200m
      memory: 1Gi
    limits:
      cpu: "2"
      memory: 4Gi
```

### Inject environment variables and secrets

```yaml
spec:
  env:
    - name: SPICE_LOG_LEVEL
      value: debug
    - name: MY_SECRET
      valueFrom:
        secretKeyRef:
          name: my-secret
          key: api-key
  envFromSource:
    - secretRef:
        name: my-secret
```

The Spicepod YAML supports `${secrets:KEY}` references to Kubernetes Secret values. Inject the backing Secret with `envFromSource` so the value resolves inside the pod at runtime.

### Add persistent storage

Attach per-replica volumes with the `volumeClaimTemplates` list; the operator creates a `PersistentVolumeClaim` per entry, per `StatefulSet`. The entry named `data` (the default when `metadata.name` is omitted) is auto-mounted at `/data`:

```yaml
spec:
  volumeClaimTemplates:
    - metadata:
        name: data
      spec:
        storageClassName: standard
        resources:
          requests:
            storage: 10Gi
```

Increasing the storage request triggers automatic PVC resizing when the `StorageClass` has `allowVolumeExpansion: true`. Shrinking is not supported.

### Use a service account (IRSA)

```yaml
spec:
  serviceAccount:
    enabled: true
    create: true
    annotations:
      eks.amazonaws.com/role-arn: arn:aws:iam::123456789012:role/spice-ai-role
```

Set `create: false` and `name:` to reuse an existing ServiceAccount.

### Restrict network traffic

A `NetworkPolicy` is **opt-in** — the operator only creates one when you supply `network.ingress` and/or `network.egress`, and writes those rules verbatim. No implicit DNS, operator-namespace, or cluster-peer rules are added, so include a DNS egress rule yourself:

```yaml
spec:
  network:
    egress:
      - to:
          - namespaceSelector:
              matchLabels:
                kubernetes.io/metadata.name: kube-system
        ports:
          - protocol: UDP
            port: 53
          - protocol: TCP
            port: 53
```

The admission webhook warns when egress omits a DNS rule. See [Network and DNS](spicepodset.md#network-and-dns) for the full schema.

### Choose an update strategy

```yaml
spec:
  replicas: 5
  updateStrategy:
    type: RollingParallel   # RollingOrdered (default) | RollingParallel | BlueGreen
    maxUnavailable: 2
```

- **`RollingOrdered`** (default) — one pod at a time, waiting for Ready.
- **`RollingParallel`** — parallel updates bounded by `maxUnavailable`.
- **`BlueGreen`** — brings up a full parallel generation, then atomically switches the Service.

The legacy `Parallel` strategy has been removed; resources specifying it are converted to `RollingOrdered`.

### Enable standby versions and instant rollback

Retain the previous generation after a rollout so traffic can be cut back instantly:

```yaml
spec:
  updateStrategy:
    type: BlueGreen
  standbyVersion:
    enabled: true
    retentionPeriodSeconds: 900
```

To roll back, **re-apply the previous `spec`** (`helm rollback`, `kubectl apply` of an earlier manifest, or a Git revert). Because the trigger is a content-based SHA of the spec, rollback is GitOps-compatible with ArgoCD and Flux — no operator-owned annotation to round-trip. See [Standby Versions & Instant Rollback](spicepodset.md#standby-versions-and-instant-rollback).

### Scale and pause

Change `replicas` to scale. Set `replicas: 0` to pause the workload while retaining the Service, ConfigMap, ServiceAccount, and any user-supplied NetworkPolicy:

```yaml
spec:
  replicas: 0
```

### Force a rollout

`annotations` and `labels` propagate to every managed resource. Changing either triggers a full rollout — a convenient restart mechanism:

```yaml
spec:
  annotations:
    rollout-trigger: "2024-06-01T12:00:00Z"
  labels:
    environment: production
```

Operator-reserved `spice.ai/*` keys are rejected by the admission controller on `spec.labels` / `spec.annotations`.

### Tune health probes and scheduling

```yaml
spec:
  probes:
    liveness:
      initialDelaySeconds: 10
      periodSeconds: 30
    readiness:
      initialDelaySeconds: 5
      periodSeconds: 10
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
        - matchExpressions:
            - key: kubernetes.io/arch
              operator: In
              values: [amd64]
  tolerations:
    - key: dedicated
      operator: Equal
      value: spice
      effect: NoSchedule
  terminationGracePeriodSeconds: 30
```

## 4. Deploy a distributed cluster

For query workloads that benefit from dedicated scheduler and executor nodes, use a `SpicepodCluster`. The operator provisions mTLS certificates and manages child `SpicepodSet`s automatically:

```yaml
apiVersion: spice.ai/v2
kind: SpicepodCluster
metadata:
  name: my-cluster
  namespace: default
spec:
  schedulerSpec:
    replicas: 1
    spicepod:
      name: my-cluster-scheduler
      kind: Spicepod
      version: v1
  executorSpec:
    replicas: 3
```

Executors pull their Spicepod configuration from the scheduler, so `executorSpec` needs no `spicepod` field. See the [SpicepodCluster reference](spicepodcluster.md) for mTLS, port separation, and per-node overrides.

## 5. Inject a sidecar into an existing Pod

To add a Spice sidecar to any existing Pod, annotate its template and point at a `ConfigMap` holding `spicepod.yaml`:

```yaml
template:
  metadata:
    annotations:
      spice.ai/inject: "true"
      spice.ai/inject-config: demo-spice-config
```

The ConfigMap must exist before the Pod is created. See [Sidecar Injection](README.md#sidecar-injection) for all supported annotations.

## 6. Monitor and inspect status

`kubectl` works natively thanks to standard status conditions:

```bash
kubectl get spicepodset
kubectl wait spicepodset/my-spicepod --for=condition=Ready

# Operator HTTP status API (port 8090)
kubectl -n spiceai-operator-system port-forward deploy/spiceai-operator 8090:8090
curl http://localhost:8090/default/my-spicepod
```

The status API returns per-pod details (phase, IP, Spiced health/readiness, error reasons). Paused `SpicepodSet`s report `paused: true` with a `pauseReason`. For operator self-telemetry over Prometheus and OTLP, see [Operator Metrics](metrics.md).

## 7. Crashloop protection

The operator monitors pods for repeated failures and, when the configured threshold is exceeded, pauses the workload (`replicas → 0`), sets `status.pauseReason = CrashLooping`, and emits a Warning event. Configure the threshold with the Helm value `pauseCrashloopingPodsThreshold` (the chart ships `0`, disabled, by default). To recover, fix the configuration and set `replicas` back to the desired count.

## 8. Upgrade the operator

```bash
helm upgrade spiceai-operator \
  oci://709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/charts/spiceai-operator \
  --namespace spiceai-operator-system \
  --values my-values.yaml
```

`v2` is served with automatic conversion of legacy resources, so existing `SpicepodSet` / `SpicepodCluster` manifests continue to apply after the upgrade.

## Next steps

- [SpicepodSet reference](spicepodset.md) — every `SpicepodSet` field.
- [SpicepodCluster reference](spicepodcluster.md) — distributed clusters and mTLS.
- [Operator Metrics](metrics.md) — Prometheus scraping and OTLP push.
- [Overview](README.md) — installation, Helm values, CLI, and roadmap.
