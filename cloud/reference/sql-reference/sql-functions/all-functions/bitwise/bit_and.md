---
description: Returns the bitwise AND of non-NULL input values.
---

# BIT\_AND

### Syntax <a href="#syntax" id="syntax"></a>

#### BIT\_AND(_expression_ int) → int <a href="#bit_andexpression-int--int" id="bit_andexpression-int--int"></a>

* expression: An expression that evaluates to a data type that can be cast as an integer.

**Examples**

{% code title="BIT_AND example" %}
```sql
SELECT BIT_AND(transaction_count)
  FROM taxi_trips 

-- EXPR$0
-- 0
```
{% endcode %}

{% code title="BIT_AND example" %}
```sql
SELECT total_gas_used, 
  BIT_AND(transaction_count)
FROM taxi_trips 
GROUP BY total_gas_used

-- total_gas_used, EXPR$1
-- 21.5, 0
-- 19.1, 0
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

If no rows match, returns a value with all bits set to 1. `NULL` values have no effect on the result unless all results are `NULL`, which is treated as no match.

\
