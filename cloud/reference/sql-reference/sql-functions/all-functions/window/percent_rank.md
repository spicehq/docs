---
description: >-
  Returns the relative rank of the current row in the partition based on the
  ORDER BY clause. The displayed percentage ranges from 0.0 to 1.0.
---

# PERCENT\_RANK

### Syntax <a href="#syntax" id="syntax"></a>

#### PERCENT\_RANK() OVER ( \[PARTITION BY partition\_expression] \[ORDER BY order\_expression]) → double <a href="#percent_rank-over--partition-by-partition_expression-order-by-order_expression--double" id="percent_rank-over--partition-by-partition_expression-order-by-order_expression--double"></a>

* partition\_expression: An optional expression that groups rows into partitions.
* order\_expression: An optional expression that specifies the order of the rows within each partition.

**Examples**

{% code title="PERCENT_RANK example" %}
```sql
SELECT "Category", 
  "Descript", 
  "DayOfWeek",
  PERCENT_RANK() 
    OVER (
      PARTITION BY "Category" 
      ORDER BY "DayOfWeek")
FROM taxi_trips 

-- Category, Descript, DayOfWeek, EXPR$3
-- ARSON, ARSON, Friday, 0.0 
-- ARSON, ARSON, Monday, 0.1368421052631579
```
{% endcode %}
