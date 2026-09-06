---
description: Spice.ai Enterprise on AWS Marketplace.
icon: aws
---

# AWS Marketplace

Spice.ai Enterprise is available on AWS Marketplace with two licensing models:

| Model    | ECR Repository                     | Description                  |
| -------- | ---------------------------------- | ---------------------------- |
| **BYOL** | `spice-ai/spiceai-enterprise-byol` | Bring Your Own License       |
| **Plan** | `spice-ai/spiceai-enterprise-plan` | AWS Marketplace subscription |

## Container Images

Enterprise images are published to the AWS Marketplace ECR registry and support both `amd64` and `arm64` architectures:

```
709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/spiceai-enterprise-byol:<version>-enterprise-models
709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/spiceai-enterprise-plan:<version>-enterprise-models
```

Tags are versioned and immutable — there is no `latest` tag. See [Docker](docker.md) for the published variants.

## Helm Chart

A Marketplace-specific Helm chart is published to the same registry, one per image variant. The chart takes the name of the ECR repository it ships in, and its version appends `-helm` to the variant tag, so the `spiceai-enterprise-byol` chart at `2.2.1-enterprise-models-helm` deploys the `2.2.1-enterprise-models` image:

```bash
helm install spiceai \
  oci://709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/spiceai-enterprise-byol \
  --version 2.2.1-enterprise-models-helm \
  --set spicepod.name=my-app
```

The chart arrives with `image.repository` and `image.tag` pre-configured for that variant. See [Helm Chart](helm-chart.md) for the full values reference.

## ECR Pull Access

AWS Marketplace grants the following ECR permissions to subscribed accounts:

- `ecr:GetDownloadUrlForLayer`
- `ecr:BatchGetImage`
- `ecr:BatchCheckLayerAvailability`
- `ecr:DescribeImages`
