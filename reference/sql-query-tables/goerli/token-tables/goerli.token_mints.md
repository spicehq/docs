---
description: SQL table schema for goerli.token_mints and goerli.recent_token_mints
---

# goerli.token\_mints

Goerli ERC-20, ERC-721, and ERC-1155 token mints.

| Column Name        | Data Type         |
| ------------------ | ----------------- |
| `name`             | CHARACTER VARYING |
| `symbol`           | CHARACTER VARYING |
| `token_address`    | CHARACTER VARYING |
| `to_address`       | CHARACTER VARYING |
| `value`            | CHARACTER VARYING |
| `transaction_hash` | CHARACTER VARYING |
| `block_number`     | BIGINT            |
| `block_timestamp`  | BIGINT            |