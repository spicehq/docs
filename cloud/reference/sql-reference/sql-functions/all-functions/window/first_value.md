---
description: Returns the first value within an ordered group of a result set.
---

# FIRST\_VALUE

### Syntax <a href="#syntax" id="syntax"></a>

#### FIRST\_VALUE(expression) OVER ( \[PARTITION BY partition\_expression] \[ORDER BY order\_expression] \[ (ASC | DESC) ] ) → same as input type <a href="#first_valueexpression-over-partition-by-partition_expression-order-by-order_expression-asc-desc" id="first_valueexpression-over-partition-by-partition_expression-order-by-order_expression-asc-desc"></a>

* expression: The expression that determines the return value.
* partition\_expression: An optional expression that groups rows into partitions. You can specify a single expression or a comma-separated list of expressions. For example, `PARTITION BY column1, column3, …`
* order\_expression: An expression that specifies the order of the rows within each partition. You can specify a single expression or a comma-separated list of expressions. For example, `PARTITION BY column1, column3, …`

**Examples**

{% code title="FIRST_VALUE example" %}
```sql
SELECT city, state, pop, 
FIRST_VALUE(pop) OVER (PARTITION BY state ORDER BY city)
FROM  taxi_trips 

-- city, state, pop, EXPR$3
-- 98791, AK, 5345, 5345
-- AKHIOK, AK, 13309, 5345
-- AKIACHAK, AK, 481, 5345
-- ... 
```
{% endcode %}
