---
description: Deploy Spice.ai Enterprise on Kubernetes in minutes.
icon: rocket
---

# Quickstart

This guide walks through deploying Spice.ai Enterprise on Kubernetes using the Spice Kubernetes Operator and Helm.

## Prerequisites

- Kubernetes 1.33.0+
- Helm 3.19.0+
- `kubectl` configured with cluster access
- AWS CLI (`aws`) installed and configured with credentials for the account subscribed to the [AWS Marketplace](../deployment/aws-marketplace.md) listing

## Step 1: Install the Spice Kubernetes Operator

The operator is distributed via the [AWS Marketplace](../deployment/aws-marketplace.md) Spice.ai Enterprise listing. Subscribe to the listing, then authenticate Helm against the Marketplace ECR registry — the chart is pulled over OCI from that private registry:

```bash
aws ecr get-login-password --region us-east-1 \
  | helm registry login --username AWS --password-stdin 709825985650.dkr.ecr.us-east-1.amazonaws.com
```

The login is valid for 12 hours; re-run it when the token expires.

Install the operator:

```bash
helm install spiceai-operator \
  oci://709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/charts/spiceai-operator
```

Verify the operator is running:

```bash
kubectl get pods -l spice.ai/app=spiceai-operator
```

Confirm the CRDs are installed:

```bash
kubectl get crd spicepodsets.spice.ai
kubectl get crd spicepodclusters.spice.ai
```

## Step 2: Deploy a SpicepodSet

Create a `spicepodset.yaml`:

```yaml
apiVersion: spice.ai/v1
kind: SpicepodSet
metadata:
  name: my-spicepod
  namespace: default
spec:
  replicas: 1
  spiceai_image_registry: 709825985650.dkr.ecr.us-east-1.amazonaws.com
  spiceai_image_name: spice-ai/spiceai-enterprise-byol
  spiceai_image_tag: 2.3.0-enterprise-models
  spicepod: |
    name: my-spicepod
    kind: Spicepod
    version: v1
```

Apply it:

```bash
kubectl apply -f spicepodset.yaml
```

## Step 3: Verify

```bash
kubectl get pods -l spice.ai/app=my-spicepod
kubectl get svc -l spice.ai/app=my-spicepod
```

The operator creates a `ClusterIP` service exposing:

| Port    | Protocol | Description         |
| ------- | -------- | ------------------- |
| `8090`  | HTTP     | HTTP/SQL API        |
| `50051` | gRPC     | Apache Arrow Flight |
| `9090`  | HTTP     | Prometheus metrics  |

## Step 4: Query

Port-forward to the service and run a query:

```bash
kubectl port-forward svc/spicepod-my-spicepod 8090:8090
```

```bash
curl http://localhost:8090/v1/sql \
  -H "Content-Type: application/json" \
  -d '{"query": "SELECT 1"}'
```

## Next Steps

- [Configure the Helm chart](../deployment/helm-chart.md) for production use.
- [Add authentication](../features/authentication.md) with OIDC or API keys.
- [Deploy a distributed cluster](../kubernetes/spicepodcluster.md) with `SpicepodCluster`.
- [Configure AWS IRSA](../deployment/aws/irsa.md) for data source access.
