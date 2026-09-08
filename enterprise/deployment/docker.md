---
description: Deploy Spice.ai Enterprise using Docker container images.
icon: docker
---

# Docker

Enterprise container images are distributed exclusively through the [AWS Marketplace ECR](aws-marketplace.md) registry. Subscribe to the Spice.ai Enterprise listing on AWS Marketplace, then authenticate with `aws ecr get-login-password` before pulling.

## Prerequisites

- Docker
- AWS CLI (`aws`) installed and configured with credentials for the account subscribed to the [AWS Marketplace](aws-marketplace.md) listing

## Images

Replace `<repo>` below with either `spiceai-enterprise-byol` (Bring Your Own License) or `spiceai-enterprise-plan` (Marketplace subscription) — see [AWS Marketplace](aws-marketplace.md) for details.

| Image                                                                              | Description                                   |
| ---------------------------------------------------------------------------------- | --------------------------------------------- |
| `709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/<repo>:latest-models`       | Default distribution with AI/ML model support |
| `709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/<repo>:latest`              | Data-only distribution                        |
| `709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/<repo>:latest-cuda`         | CUDA GPU-accelerated distribution             |
| `709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/<repo>:latest-nas`          | NAS distribution (SMB + NFS connectors)       |
| `709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/<repo>:latest-jemalloc`     | jemalloc memory allocator variant             |
| `709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/<repo>:latest-mimalloc`     | mimalloc memory allocator variant             |
| `709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/<repo>:latest-sysalloc`     | System (glibc) memory allocator variant       |

## Pull an Image

```bash
aws ecr get-login-password --region us-east-1 \
  | docker login --username AWS --password-stdin 709825985650.dkr.ecr.us-east-1.amazonaws.com

docker pull 709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/spiceai-enterprise-byol:latest-models
```

## Run

```bash
docker run -p 8090:8090 -p 50051:50051 -p 9090:9090 \
  709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/spiceai-enterprise-byol:latest-models \
  --http 0.0.0.0:8090 \
  --metrics 0.0.0.0:9090 \
  --flight 0.0.0.0:50051
```

### With a Spicepod Configuration

Mount a Spicepod YAML file into the container:

```bash
docker run -p 8090:8090 -p 50051:50051 -p 9090:9090 \
  -v $(pwd)/spicepod.yaml:/app/spicepod.yaml \
  709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/spiceai-enterprise-byol:latest-models
```

## Exposed Ports

| Port    | Protocol | Description         |
| ------- | -------- | ------------------- |
| `8090`  | HTTP     | HTTP/SQL API        |
| `50051` | gRPC     | Apache Arrow Flight |
| `9090`  | HTTP     | Prometheus metrics  |

## Image Details

Enterprise images are built `FROM scratch` with a minimal filesystem containing:

- The `spiced` binary
- CA certificates
- Timezone database
- Required shared libraries

The container runs as UID 65534 (nobody) for security.

### Environment Variables

| Variable        | Default                              |
| --------------- | ------------------------------------ |
| `HOME`          | `/app`                               |
| `HF_HOME`       | `/.cache/huggingface`                |
| `SSL_CERT_FILE` | `/etc/ssl/certs/ca-certificates.crt` |
| `SSL_CERT_DIR`  | `/etc/ssl/certs`                     |
