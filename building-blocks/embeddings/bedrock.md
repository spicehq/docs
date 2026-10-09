---
description: 'Instructions for using Amazon Bedrock embedding models'
---

# Amazon Bedrock Embedding Models

To use an embedding model deployed to [AWS Bedrock service](https://aws.amazon.com/bedrock/), specify the model endpoint name prefixed with `bedrock:` in the `from` field and include the required parameters in the `params` section.

### Parameters

#### AWS Parameters

| Parameter                    | Description                                                                                                                             |
| ---------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| `aws_region`                 | AWS region. Default: `us-east-1`.                                                                                                       |
| `aws_profile`                | Optional. AWS profile to use when loading credentials.                                                                                  |
| `aws_access_key_id`          | Optional. AWS access key ID for authentication. If not provided, credentials will be loaded from environment variables or IAM roles     |
| `aws_secret_access_key`      | Optional. AWS secret access key for authentication. If not provided, credentials will be loaded from environment variables or IAM roles |
| `aws_session_token`          | Optional. AWS session token for authentication                                                                                          |
| `max_concurrent_invocations` | Optional. The maximum number of concurrent API invocations. Defaults to `40`                                                            |
| `requests_per_min_limit`     | Optional. The maximum number of requests made per minute. Defaults to `1500`                                                            |

#### AWS Titan Models

These parameters are used for [Amazon Titan Text](https://docs.aws.amazon.com/bedrock/latest/userguide/titan-embedding-models.html) embedding model

| Parameter    | Description                                                                                                             |
| ------------ | ----------------------------------------------------------------------------------------------------------------------- |
| `normalize`  | Whether or not to normalize the output embedding. Defaults to `true`.                                                   |
| `dimensions` | The number of dimensions the output embedding should have. The following values are accepted: 1024 (default), 512, 256. |

#### Amazon Nova Models

These parameters are used for [Amazon Nova](https://docs.aws.amazon.com/nova/latest/userguide/embeddings-schema.html) multimodal embedding models

| Parameter           | Description                                                                                                                                                                                                        |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `dimensions`        | **Required**. The number of dimensions the output embedding should have. Accepted value: 256, 384, 1024 or 3072.                                                                                                   |
| `truncation_mode`   | Optional. Specifies how the API handles inputs longer than the maximum token length. One of: `START`, `END` or `NONE` (default).                                                                                   |
| `embedding_purpose` | Optional. Use the Nova embeddings model optimized for different purposes. Default `GENERIC_INDEX`. See reference [docs](https://docs.aws.amazon.com/nova/latest/userguide/embeddings-schema.html) for all options. |

#### Cohere Models

| Parameter    | Description                                                                                                                                                     |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `truncate`   | Specifies how the API handles inputs longer than the maximum token length. One of: `START`, `END` or `NONE` (default).                                          |
| `input_type` | Use the Cohere embeddings model optimized for different types of inputs. One of: `search_document` (default), `search_query`, `classification` or `clustering`. |

### Example `spicepod.yaml` configuration, Cohere model

```yaml
embeddings:
  - from: bedrock:cohere.embed-english-v3
    name: cohere-embeddings
    params:
      aws_region: us-east-1
      input_type: classification
      truncate: END
      aws_access_key_id: ${ secrets:AWS_ACCESS_KEY_ID }
      aws_secret_access_key: ${ secrets:AWS_SECRET_ACCESS_KEY }
```

### Example `spicepod.yaml` configuration, Titan model

```yaml
- from: bedrock:amazon.titan-embed-text-v2:0
  name: titan-embeddings
  params:
    dimensions: '256'
```

### Example `spicepod.yaml` configuration, Amazon Nova model

```yaml
embeddings:
  - from: bedrock:amazon.nova-2-multimodal-embeddings-v1:0
    name: nova-embeddings
    params:
      dimensions: '3072'
      truncation_mode: START
      embedding_purpose: GENERIC_RETRIEVAL
      aws_region: us-east-1
      aws_access_key_id: ${ secrets:AWS_ACCESS_KEY_ID }
      aws_secret_access_key: ${ secrets:AWS_SECRET_ACCESS_KEY }
```

{% hint style="info" %}
**IAM Permissions:** IAM role or user must have appropriate bedrock permissions (e.g., `bedrock:InvokeModel`) to access the model. If the Spicepod connects to multiple different AWS services, the permissions should cover all of them.
{% endhint %}

## Required IAM Permissions

The IAM role or user needs the following permissions to access Bedrock models:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["bedrock:InvokeModel"],
      "Resource": ["arn:aws:bedrock:us-east-1::foundation-model/amazon.titan-*"]
    }
  ]
}
```

### Permission Details

| Permission            | Purpose                                       |
| --------------------- | --------------------------------------------- |
| `bedrock:InvokeModel` | Required. Used to invoke the embedding model. |

### Additional Information

Refer to the [Amazon Bedrock documentation](https://docs.aws.amazon.com/bedrock/) for more details on available models and configurations.
