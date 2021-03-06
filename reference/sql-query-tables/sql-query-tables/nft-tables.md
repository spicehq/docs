# NFT Tables

#### NFT specific tables

| Table Name                         | Description                                                                                                     |
| ---------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| `eth.nfts`                         | All NFTs from both ERC721 and ERC1155 contracts.                                                                |
| `eth.nft_contracts`                | All erc721 contracts & subset of erc1155 contracts that contain NFTs.                                           |
| `eth.nft_transfers`                | <p>Transfers of erc721 &#x26; erc1155 NFTs. </p><p>Does not include fungible tokens from erc1155 contracts.</p> |
| `eth.nft_owners`                   | Tracks the current owner of an NFT.                                                                             |
| `eth.recent_nft_transfers`         | Transfers of NFTs from the last 30 minutes, \~128 blocks                                                        |
| `eth.nft_airdrop_transfers`        | Airdrops of NFTs                                                                                                |
| `eth.recent_nft_airdrop_transfers` | Airdrops of NFTs from the last 30 minutes, \~128 blocks                                                         |

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

| Table Name                  | Indexed Columns |
| --------------------------- | --------------- |
| `eth.nft_transfers`         | `block_number`  |
| `eth.nft_airdrop_transfers` | `block_number`  |
