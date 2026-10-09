---
description: Configure IAM Roles for Service Accounts (IRSA) for Spice.ai on Amazon EKS.
---

# AWS IRSA (EKS)

IAM Roles for Service Accounts (IRSA) allows Spice.ai pods on Amazon EKS to assume an IAM role for accessing AWS services (S3, Secrets Manager, DynamoDB, etc.) without managing static credentials.

## How It Works

The AWS SDK credential provider chain automatically uses **STS Web Identity Token Credentials** when a pod runs under an IRSA-annotated `ServiceAccount`. The SDK calls `sts:AssumeRoleWithWebIdentity` to retrieve temporary credentials.

Credential chain order:

1. Environment variables
2. Shared credentials/config files
3. **STS Web Identity (IRSA)**
4. ECS container credentials
5. EC2 instance metadata (IMDSv2)

## Prerequisites

- An EKS cluster with an [OIDC provider](https://docs.aws.amazon.com/eks/latest/userguide/enable-iam-roles-for-service-accounts.html)
- An IAM role with the correct trust policy
- The Spice Kubernetes Operator installed via Helm

## Step 1: Create IAM Role with IRSA Trust Policy

```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Principal": {
      "Federated": "arn:aws:iam::ACCOUNT_ID:oidc-provider/oidc.eks.REGION.amazonaws.com/id/CLUSTER_ID"
    },
    "Action": "sts:AssumeRoleWithWebIdentity",
    "Condition": {
      "StringEquals": {
        "oidc.eks.REGION.amazonaws.com/id/CLUSTER_ID:sub": "system:serviceaccount:NAMESPACE:SERVICE_ACCOUNT_NAME"
      }
    }
  }]
}
```

Replace `ACCOUNT_ID`, `REGION`, `CLUSTER_ID`, `NAMESPACE`, and `SERVICE_ACCOUNT_NAME` with your values.

## Step 2: Attach IAM Policies

Attach policies for the AWS services your Spicepod connects to:

| Use Case        | Required IAM Actions                                  |
| --------------- | ----------------------------------------------------- |
| S3 data sources | `s3:GetObject`, `s3:ListBucket`                       |
| Secrets Manager | `secretsmanager:GetSecretValue`                       |
| DynamoDB        | `dynamodb:GetItem`, `dynamodb:Query`, `dynamodb:Scan` |

## Step 3: Configure the SpicepodSet

### Operator-Managed ServiceAccount

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
      eks.amazonaws.com/role-arn: "arn:aws:iam::123456789012:role/spice-ai-role"
  spicepod: |
    name: my-spicepod
    kind: Spicepod
    version: v1
```

### Using an Existing ServiceAccount

```yaml
spec:
  service_account:
    enabled: true
    create: false
    name: my-irsa-service-account
```

### Helm Chart (Enterprise)

```yaml
serviceAccount:
  create: true
  name: spiceai
  annotations:
    eks.amazonaws.com/role-arn: arn:aws:iam::123456789012:role/my-spice-role
```

## Operator ServiceAccount via Helm

To grant the operator itself AWS access (for example, to pull images from ECR):

```yaml
# Helm values for the operator chart
serviceAccount:
  annotations:
    eks.amazonaws.com/role-arn: "arn:aws:iam::123456789012:role/spice-operator-role"
```

## EKS Pod Identity (Alternative)

[EKS Pod Identity](https://docs.aws.amazon.com/eks/latest/userguide/pod-identities.html) is a simpler alternative to IRSA that uses the EKS Pod Identity Agent add-on. The IAM role association is managed via the EKS API — no ServiceAccount annotation is needed.
