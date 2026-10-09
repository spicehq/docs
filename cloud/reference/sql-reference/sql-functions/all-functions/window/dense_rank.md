---
description: >-
  Returns the rank of the current row within its partition and ordering. Rows
  that are equal will have the same rank.
---

# DENSE\_RANK

### Syntax <a href="#syntax" id="syntax"></a>

#### DENSE\_RANK() OVER ( \[PARTITION BY partition\_expression] \[ORDER BY order\_expression]) → bigint <a href="#dense_rank-over--partition-by-partition_expression-order-by-order_expression--bigint" id="dense_rank-over--partition-by-partition_expression-order-by-order_expression--bigint"></a>

* partition\_expression: An optional expression that groups rows into partitions.
* order\_expression: An optional expression that specifies the order of the rows within each partition.

**Examples**

{% code title="DENSE_RANK example" %}
```sql
SELECT "Category", 
  "Descript", 
  "DayOfWeek",
  DENSE_RANK() 
    OVER (
      PARTITION BY "Category" 
      ORDER BY "DayOfWeek")
FROM taxi_trips 

-- Category, Descript, DayOfWeek, EXPR$3
-- ARSON, ARSON, Friday, 1
-- ARSON, ARSON, Monday, 2
```
{% endcode %}
