# Uniswap V2 Tables

### Tables available to query

| Name                                 | Description                                                   |
| ------------------------------------ | ------------------------------------------------------------- |
| `eth.uniswap_v2.pools`               | Top 1000 Uniswap-V2 Pools                                     |
| `eth.uniswap_v2.pool_stats`          | Pool stats (reserves, etc) for each block                     |
| `eth.uniswap_v2.pool_stats_detailed` | Join between pools and pool\_stats                            |
| `eth.uniswap_v2.event_mints`         | Uniswap-V2 mint events                                        |
| `eth.uniswap_v2.event_burns`         | Uniswap-V2 burn events                                        |
| `eth.uniswap_v2.event_swaps`         | Uniswap-V2 swap events                                        |
| `eth.uniswap_v2.event_syncs`         | Uniswap-V2 sync events                                        |
| `eth.uniswap_v2.recent_event_mints`  | Uniswap-V2 mint events from the last 30 minutes, \~128 blocks |
| `eth.uniswap_v2.recent_event_burns`  | Uniswap-V2 burn events from the last 30 minutes, \~128 blocks |
| `eth.uniswap_v2.recent_event_swaps`  | Uniswap-V2 swap events from the last 30 minutes, \~128 blocks |
| `eth.uniswap_v2.recent_event_syncs`  | Uniswap-V2 sync events from the last 30 minutes, \~128 blocks |

The columns and their schema available for each table can be viewed with the `describe <table>` command. For example:

```sql
DESCRIBE eth.uniswap_v2.pools
DESCRIBE eth.uniswap_v2.pool_stats
DESCRIBE eth.uniswap_v2.pool_stats_detailed
DESCRIBE eth.uniswap_v2.event_mints
```

#### Improving query performance - indexed columns

Query performance can be significantly improved by adding `WHERE` clauses to your query on specific indexed columns.

| Table Name                                       | Indexed Columns                            |
| ------------------------------------------------ | ------------------------------------------ |
| `eth.uniswap_v2.pool_stats`                      | `block_number` `pool_address`              |
| `eth.uniswap_v2.pool_stats_detailed`             | `block_number` `pool_address`              |
| `eth.uniswap_v2.event_[mints/burns/swaps/syncs]` | `block_number` `block_timestamp` `address` |