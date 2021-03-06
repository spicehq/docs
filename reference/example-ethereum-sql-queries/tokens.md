# Tokens

### Get Token Transfers with Token Standard

Get recent token transfers along with the token standard the token is using.

```sql
SELECT token_address, 
       token_standard, 
       from_address, 
       to_address, 
       token_id, 
       "value", 
       transaction_hash, 
       log_index, 
       block_number 
FROM eth.recent_token_transfers 
ORDER BY block_number DESC
LIMIT 10
```

### Get Tokens With Standard

Use the tokens table to get information about the token standard that the token is using.

**Typical query time**: <20 seconds

```sql
SELECT symbol, name, is_erc20, is_erc721, is_erc1155
FROM eth.tokens
WHERE name != ''
ORDER BY name
LIMIT 10
```

### Get Confidence for Token Standards

Get Spice's confidence (as a score from 0 to 1) on whether a smart contract implements the given token standard.

```sql
SELECT address, erc20_confidence, erc721_confidence, erc1155_confidence
FROM eth.contracts
WHERE erc20_confidence > 0 OR erc721_confidence > 0 OR erc1155_confidence > 0
LIMIT 10
```
