---
description: >-
  Returns the row before the current one in a partition based on the ORDER BY
  clause without the need for a self-join. If there are no rows, this function
  returns NULL.
---

# LAG

### Syntax <a href="#syntax" id="syntax"></a>

#### LAG(expression, \[offset]) OVER (\[PARTITION BY partition\_expression] \[ORDER BY order\_expression]) → same as input type <a href="#lagexpression-offset-over-partition-by-partition_expression-order-by-order_expression--same-as-input" id="lagexpression-offset-over-partition-by-partition_expression-order-by-order_expression--same-as-input"></a>

* expression: An expression that is returned.
* offset: An optional parameter. The number of rows before the current row from which to obtain a value. Supports only a value of 1.
* partition\_expression: An optional expression that groups rows into partitions.
* order\_expression: An optional expression that specifies the order of the rows within each partition.

**Examples**

{% code title="LAG example" %}
```sql
SELECT "Category", 
  "Descript", 
  "DayOfWeek",
  LAG(DayOfWeek, 1) 
    OVER (
      PARTITION BY "Category" 
      ORDER BY "DayOfWeek")
FROM  taxi_trips 

-- Category, Descript, DayOfWeek, EXPR$3
-- ARSON, ARSON, Friday, null 
-- ARSON, ARSON, Friday, Friday
```
{% endcode %}
