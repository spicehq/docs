---
description: Configure GKE Workload Identity for Spice.ai on Google Kubernetes Engine.
---

# GKE Workload Identity

GKE Workload Identity allows Spice.ai pods on Google Kubernetes Engine to authenticate as a Google Cloud service account for accessing GCP services (GCS, BigQuery, Secret Manager, etc.).

## Configuration

Annotate the Spice `ServiceAccount` with the GCP service account email:

### Helm Chart

```yaml
serviceAccount:
  create: true
  annotations:
    iam.gke.io/gcp-service-account: my-gcp-sa@my-project.iam.gserviceaccount.com
```

### SpicepodSet (Kubernetes Operator)

```yaml
apiVersion: spice.ai/v1
kind: SpicepodSet
metadata:
  name: my-spicepod
spec:
  replicas: 1
  service_account:
    enabled: true
    create: true
    annotations:
      iam.gke.io/gcp-service-account: my-gcp-sa@my-project.iam.gserviceaccount.com
  spicepod: |
    name: my-spicepod
    kind: Spicepod
    version: v1
```

## Prerequisites

1. Enable Workload Identity on your GKE cluster.
2. Create a GCP service account with the required IAM roles.
3. Bind the Kubernetes `ServiceAccount` to the GCP service account:

```bash
gcloud iam service-accounts add-iam-policy-binding \
  my-gcp-sa@my-project.iam.gserviceaccount.com \
  --role roles/iam.workloadIdentityUser \
  --member "serviceAccount:my-project.svc.id.goog[NAMESPACE/SERVICE_ACCOUNT_NAME]"
```
