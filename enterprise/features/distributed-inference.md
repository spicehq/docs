---
description: Multi-node tensor-parallel LLM inference for Spice.ai Enterprise, splitting a single model across nodes over a pure-TCP ring backend.
icon: microchip
---

# Distributed Inference

Spice.ai Enterprise can serve a single language model across multiple nodes using **tensor parallelism**. A model whose weights do not fit in one node's memory is split across nodes, which exchange activations over a pure-TCP **ring** all-reduce backend. The ring backend needs no NCCL or other system dependency.

{% hint style="info" %}
Multi-node distributed inference is a **Spice.ai Enterprise** feature and requires the `distributed` build. Single-node model serving — including `model_type: glm4` — is available in Spice.ai OSS. See [Hugging Face model provider](../../building-blocks/model-providers/huggingface.md).
{% endhint %}

## Configuration

Run the same model on every node, changing only `node_rank`. Every node is given the identical `nodes` list, ordered by rank; rank `0` is the head node and serves the API.

```yaml
models:
  - name: glm
    from: huggingface:huggingface.co/THUDM/glm-4-9b-chat
    params:
      model_type: glm4              # glm4, glm4moe, or glm4moelite
      distributed_backend: ring     # none (default, single-node) | ring
      nodes: 10.0.4.21,10.0.4.22    # identical on every node, ordered by rank
      node_rank: 0                  # this node's rank; set node_rank: 1 on 10.0.4.22
```

| Parameter             | Description                                                                                          |
| --------------------- | ---------------------------------------------------------------------------------------------------- |
| `distributed_backend` | `none` (default, single-node) or `ring` (multi-node tensor parallelism over TCP).                    |
| `nodes`               | Comma-separated, rank-ordered list of node host/IPs. The same list is set on every node.             |
| `node_rank`           | This node's rank in `[0, world_size)`. Rank `0` is the head node and serves the API.                 |

The **world size** is the number of entries in `nodes`.

## Constraints

- At least **2 nodes** are required.
- The world size must be a **power of two**.
- The `ring` backend currently supports **exactly 2 nodes** (`world_size = 2`).
- `node_rank` must be within `[0, world_size)`.
- The world size must divide the model's attention / KV head counts; this is enforced when the model loads.

## See also

- [Distributed Query](distributed-query.md)
- [High Availability](../production/high-availability.md)
