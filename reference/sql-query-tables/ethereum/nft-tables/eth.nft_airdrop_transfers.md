---
description: SQL table schema for eth.nft_airdrop_transfers
---

# eth.nft\_airdrop\_transfers

Ethereum Airdrops of NFTs.

| Column Name        | Data Type         |
| ------------------ | ----------------- |
| `token_address`    | CHARACTER VARYING |
| `token_id`         | CHARACTER VARYING |
| `tx_from_address`  | CHARACTER VARYING |
| `tx_value`         | DECIMAL           |
| `from_address`     | CHARACTER VARYING |
| `to_address`       | CHARACTER VARYING |
| `transaction_hash` | CHARACTER VARYING |
| `block_number`     | BIGINT            |
| `block_timestamp`  | BIGINT            |
| `block_hash`       | CHARACTER VARYING |