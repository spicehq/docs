---
description: Returns the total number of records for the specified expression.
---

# COUNT

### Syntax <a href="#syntax" id="syntax"></a>

#### COUNT(_expression_ any primitive) → bigint <a href="#countexpression-any-primitive--bigint" id="countexpression-any-primitive--bigint"></a>

* expression: The expression to evaluate the number of records. This parameter can either be \* or a column name of any primitive data type. Using asterisk (\*) will include rows that contain `NULL`. Specifying a column name will ignore rows have `NULL` in that column.

**Examples**

{% code title="Aggregate function example" %}
```sql
SELECT COUNT(transaction_count) 
FROM taxi_trips
-- 338293677
```
{% endcode %}

{% code title="Window function example" %}
```sql
SELECT "base_fee_per_gas",
  COUNT("total_gas_used") 
  OVER(PARTITION BY "base_fee_per_gas") "count_total_gas_used"
FROM taxi_trips
LIMIT 1

-- base_fee_per_gas, count_total_gas_used
-- 0.06, 61900
```
{% endcode %}
