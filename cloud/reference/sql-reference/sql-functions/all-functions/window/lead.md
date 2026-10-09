---
description: >-
  Returns the row after the current one in the same result set without the need
  for a self-join. If there are no rows, this function returns NULL.
---

# LEAD

### Syntax <a href="#syntax" id="syntax"></a>

#### LEAD(expression, \[offset]) OVER (\[PARTITION BY partition\_expression] \[ORDER BY order\_expression]) → same as input type <a href="#leadexpression-offset-over-partition-by-partition_expression-order-by-order_expression--same-as-inpu" id="leadexpression-offset-over-partition-by-partition_expression-order-by-order_expression--same-as-inpu"></a>

* expression: An expression that is returned.
* offset: An optional parameter. The number of rows after the current row from which to obtain a value. Supports only a value of 1.
* partition\_expression: An optional expression that groups rows into partitions.
* order\_expression: An optional expression that specifies the order of the rows within each partition.

**Examples**

{% code title="LEAD example" %}
```sql
SELECT "Category", 
  "Descript", 
  "DayOfWeek",
  LEAD(DayOfWeek, 1) 
    OVER (
      PARTITION BY "Category" 
      ORDER BY "DayOfWeek")
FROM taxi_trips 

-- Category, Descript, DayOfWeek, EXPR$3
-- ARSON, ARSON, Friday, Friday 
-- ARSON, ARSON OF A VEHICLE, Wednesday, null
```
{% endcode %}
