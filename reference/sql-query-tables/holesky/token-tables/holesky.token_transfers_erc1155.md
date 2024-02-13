---
description: SQL table schema for holesky.token_transfers_erc1155
---

# holesky.token\_transfers\_erc1155

Holesky ERC-1155 token transfers.

| Column Name        | Data Type         | Description                                                          |
| ------------------ | ----------------- | -------------------------------------------------------------------- |
| `token_address`    | CHARACTER VARYING | The contract address of the token being transferred.                 |
| `operator`         | CHARACTER VARYING | The address of the operator contract, for proxied transfers.         |
| `from_address`     | CHARACTER VARYING | The address of the sender of this transfer.                          |
| `to_address`       | CHARACTER VARYING | The address of the recipient of this transfer.                       |
| `token_id`         | CHARACTER VARYING | The unique instance id of the specific token mint being transferred. |
| `value`            | CHARACTER VARYING | The number of tokens transferred.                                    |
| `value_hex`        | CHARACTER VARYING | The value, encoded as a hexadecimal string.                          |
| `transaction_hash` | CHARACTER VARYING | The hash of the transaction in which this transfer occurred.         |
| `log_index`        | BIGINT            | The index of this transfer within the transaction.                   |
| `block_timestamp`  | BIGINT            | The timestamp of the block in which this transfer occurred.          |
| `block_number`     | BIGINT            | The number of the block in which this transfer occurred.             |
| `block_hash`       | CHARACTER VARYING | The hash of the block in which this transfer occurred.               |
| `batch_index`      | BIGINT            | The batch number of this transfer, for large batched transfers.      |