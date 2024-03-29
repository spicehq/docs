---
description: Ethereum NFT tables available to query via SQL
---

# NFT Tables

#### NFT specific tables

| Table Name                                                           | Description                                                                                                                                                                  |
| -------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [`eth.nfts`](eth.nfts.md)                                            | All NFTs from both ERC721 and ERC1155 contracts.                                                                                                                             |
| [`eth.nft_contracts`](eth.nft\_contracts.md)                         | All erc721 contracts & subset of erc1155 contracts that contain NFTs.                                                                                                        |
| [`eth.nft_transfers`](eth.nft\_transfers.md)                         | <p>Transfers of erc721 &#x26; erc1155 NFTs.</p><p>Does not include fungible tokens from erc1155 contracts.</p>                                                               |
| [`eth.nft_owners`](eth.nft\_owners.md)                               | <p>Tracks the current owner of an NFT.<br>This table is mutable; the number of rows within a given time or block range can be different due to changes in NFT ownership.</p> |
| [`eth.recent_nft_transfers`](eth.recent\_nft\_transfers.md)          | Transfers of NFTs from the last 30 minutes, \~128 blocks                                                                                                                     |
| [`eth.nft_airdrop_transfers`](eth.nft\_airdrop\_transfers.md)        | Airdrops of NFTs                                                                                                                                                             |
| [`eth.recent_nft_airdrop_transfers`](eth.nft\_airdrop\_transfers.md) | Airdrops of NFTs from the last 30 minutes, \~128 blocks                                                                                                                      |

The columns and their schema available for each table can be viewed with the `describe <table>` command. For example:

```sql
/* Show the columns available */
DESCRIBE eth.nfts;
DESCRIBE eth.nft_contracts;
DESCRIBE eth.nft_transfers;
DESCRIBE eth.nft_owners;
DESCRIBE eth.recent_nft_transfers;
DESCRIBE eth.nft_airdrop_transfers;
DESCRIBE eth.recent_nft_airdrop_transfers;
```

#### Improving query performance - indexed columns

Query performance can be significantly improved by adding `WHERE` clauses to your query on specific indexed columns.

| Table Name                         | Indexed Columns                                      |
| ---------------------------------- | ---------------------------------------------------- |
| `eth.nft_transfers`                | `block_number`                                       |
| `eth.nft_airdrop_transfers`        | `block_number`                                       |
| `eth.nfts`                         |                                                      |
| `eth.nft_contracts`                | `address`                                            |
| `eth.nft_owners`                   | `token_address` `token_id` `owner` `block_timestamp` |
| `eth.recent_nft_transfers`         |                                                      |
| `eth.recent_nft_airdrop_transfers` |                                                      |
