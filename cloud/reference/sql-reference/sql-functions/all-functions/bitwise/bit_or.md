---
description: Returns the bitwise OR of non-NULL input values.
---

# BIT\_OR

### Syntax <a href="#syntax" id="syntax"></a>

#### BIT\_OR(_expression_ int) → int <a href="#bit_orexpression-int--int" id="bit_orexpression-int--int"></a>

* expression: An expression that evaluates to a data type that can be cast as an integer.

**Examples**

{% code title="BIT_OR example" %}
```sql
SELECT BIT_OR(passenger_count)
FROM taxi_trips 
  
-- EXPR$0
-- 255
```
{% endcode %}

{% code title="BIT_OR example" %}
```sql
SELECT total_gas_used, 
  BIT_OR(transaction_count)
FROM taxi_trips 
GROUP BY total_gas_used

-- total_gas_used, EXPR$1
-- 29.0, 7
-- 5.5, 15
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

If no rows match, returns a value with all bits set to 0. NULL values have no effect on the result unless all results are NULL, which is treated as no match.

\
