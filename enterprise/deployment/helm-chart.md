---
description: Deploy Spice.ai Enterprise on Kubernetes with the official Helm chart.
icon: dharmachakra
---

# Helm Chart

The Spice.ai Enterprise Helm chart deploys the Spice runtime as a Kubernetes `Deployment` or `StatefulSet` with a `ConfigMap`-mounted Spicepod configuration.

## Install

The chart is published to the AWS Marketplace ECR registry, one chart per image variant, with the image pre-configured. Subscribe to the Enterprise listing first — see [AWS Marketplace](aws-marketplace.md) — then authenticate Helm against the registry:

```bash
aws ecr get-login-password --region us-east-1 \
  | helm registry login --username AWS --password-stdin 709825985650.dkr.ecr.us-east-1.amazonaws.com
```

The login is valid for 12 hours; re-run it when the token expires.

```bash
helm install spiceai \
  oci://709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/spiceai-enterprise-byol \
  --version 2.2.1-enterprise-models-helm \
  --set spicepod.name=my-app
```

## Values Reference

| Parameter                                | Description                                       | Default                              |
| ---------------------------------------- | ------------------------------------------------- | ------------------------------------ |
| `image.repository`                       | Container image repository                        | `709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/spiceai-enterprise-byol` |
| `image.tag`                              | Container image tag                               | The chart variant's image tag, such as `2.2.1-enterprise-models` |
| `replicaCount`                           | Number of replicas                                | `1`                                  |
| `strategy`                               | Update strategy for the `Deployment`              | — (Kubernetes default)               |
| `updateStrategy`                         | Update strategy for the `StatefulSet`             | — (Kubernetes default)               |
| `serviceAccount.create`                  | Create a `ServiceAccount`                         | `false`                              |
| `serviceAccount.name`                    | `ServiceAccount` name                             | —                                    |
| `serviceAccount.annotations`             | Annotations for the `ServiceAccount`              | `{}`                                 |
| `service.type`                           | Kubernetes Service type                           | —                                    |
| `service.additionalAnnotations`          | Annotations for the Service                       | `{}`                                 |
| `stateful.enabled`                       | Use a `StatefulSet` with PVC                      | `false`                              |
| `stateful.storageClass`                  | `StorageClass` for `StatefulSet` PVC              | `standard`                           |
| `stateful.size`                          | PVC size                                          | `1Gi`                                |
| `stateful.mountPath`                     | Mount path in container                           | `/data`                              |
| `monitoring.podMonitor.enabled`          | Create a `PodMonitor` for Prometheus              | `false`                              |
| `monitoring.podMonitor.additionalLabels` | Labels for the `PodMonitor`                       | `{}`                                 |
| `additionalLabels`                       | Labels added to all resources                     | `{}`                                 |
| `additionalEnv`                          | Extra environment variables                       | `[]`                                 |
| `resources`                              | CPU/memory requests and limits                    | `{}`                                 |
| `volumes`                                | Additional volumes                                | `[]`                                 |
| `volumeMounts`                           | Additional volume mounts                          | `[]`                                 |
| `spicepod`                               | Spicepod configuration (inlined into `ConfigMap`) | —                                    |

## Spicepod Configuration

The Spicepod spec is inlined directly in the Helm values and mounted as a `ConfigMap`:

```yaml
spicepod:
  name: my-app
  version: v1
  kind: Spicepod
  datasets:
    - from: s3://my-bucket/data/
      name: my_data
      params:
        file_format: parquet
      acceleration:
        enabled: true
        engine: duckdb
```

## StatefulSet with Persistent Storage

Enable a `StatefulSet` with a `PersistentVolumeClaim` for durable storage:

```yaml
stateful:
  enabled: true
  storageClass: gp3
  size: 50Gi
  mountPath: /data
```

## Update Strategy

Both keys are unset by default, so Kubernetes applies its own default strategy. Which one the chart reads depends on `stateful.enabled`.

With `stateful.enabled: false` the chart renders a `Deployment` and reads `strategy`:

```yaml
strategy:
  type: Recreate
```

With `stateful.enabled: true` the chart renders a `StatefulSet` and reads `updateStrategy`:

```yaml
updateStrategy:
  type: RollingUpdate
```

The key that does not match `stateful.enabled` is ignored, so setting `strategy` on a `StatefulSet` deployment has no effect. Each value is passed through to the workload spec unchanged, so any field the Kubernetes strategy type accepts — `rollingUpdate.maxUnavailable`, `rollingUpdate.partition` — can be set alongside `type`.

## Resource Limits

```yaml
resources:
  requests:
    cpu: 200m
    memory: 1Gi
  limits:
    cpu: "2"
    memory: 4Gi
```

## Prometheus Monitoring

```yaml
monitoring:
  podMonitor:
    enabled: true
    additionalLabels:
      release: prometheus
```

## ServiceAccount for Cloud IAM

### AWS IRSA (EKS)

```yaml
serviceAccount:
  create: true
  name: spiceai
  annotations:
    eks.amazonaws.com/role-arn: arn:aws:iam::123456789012:role/my-spice-role
```

See [AWS IRSA](aws/irsa.md) for full setup instructions.

### GKE Workload Identity

```yaml
serviceAccount:
  create: true
  annotations:
    iam.gke.io/gcp-service-account: my-gcp-sa@my-project.iam.gserviceaccount.com
```

### Azure Workload Identity (AKS)

```yaml
serviceAccount:
  create: true
  annotations:
    azure.workload.identity/client-id: <AZURE_CLIENT_ID>
```
