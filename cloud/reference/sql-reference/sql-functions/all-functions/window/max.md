---
description: Returns the maximum value among the non-NULL input expressions.
---

# MAX

### Syntax <a href="#syntax" id="syntax"></a>

#### MAX(_expression_ any primitive) → same as input <a href="#maxexpression-any-primitive--same-as-input" id="maxexpression-any-primitive--same-as-input"></a>

* expression: Input expression.

**Examples**

{% code title="Aggregate function example" %}
```sql
SELECT MAX("total_gas_used") 
FROM taxi_trips

-- EXPR$0
-- 685908.1
```
{% endcode %}

#### MAX(_expression_ any primitive) → same as input <a href="#maxexpression-any-primitive--same-as-input-1" id="maxexpression-any-primitive--same-as-input-1"></a>

* expression: Input expression.

**Examples**

{% code title="Window function example" %}
```sql
SELECT "transaction_count", 
  MAX("total_gas_used") OVER(PARTITION BY "transaction_count") "max_total_gas_used"
FROM taxi_trips
LIMIT 1

-- trip_distance_mi, max_total_amount
-- 0.03, 450.5
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

For information about the data types that are supported in Spice, see Data Types.
