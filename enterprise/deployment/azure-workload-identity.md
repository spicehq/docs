---
description: Configure Azure Workload Identity for Spice.ai on AKS.
---

# Azure Workload Identity

Azure Workload Identity allows Spice.ai pods on Azure Kubernetes Service (AKS) to authenticate with Azure services (Blob Storage, Key Vault, etc.) using federated credentials.

## Configuration

Annotate the Spice `ServiceAccount` with the Azure managed identity client ID:

### Helm Chart

```yaml
serviceAccount:
  create: true
  annotations:
    azure.workload.identity/client-id: <AZURE_CLIENT_ID>
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
      azure.workload.identity/client-id: <AZURE_CLIENT_ID>
  spicepod: |
    name: my-spicepod
    kind: Spicepod
    version: v1
```

## Prerequisites

1. Enable the Azure Workload Identity add-on on your AKS cluster.
2. Create a managed identity and federated credential.
3. Assign the necessary roles to the managed identity for the Azure resources your Spicepod accesses.

See [Azure Workload Identity documentation](https://learn.microsoft.com/en-us/azure/aks/workload-identity-overview) for full setup instructions.
