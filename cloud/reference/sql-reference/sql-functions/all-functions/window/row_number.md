---
description: >-
  Returns the row number for the current row based on the ORDER BY clause within
  each partition. Rows containing identical values receive different row
  numbers.
---

# ROW\_NUMBER

### Syntax <a href="#syntax" id="syntax"></a>

#### ROW\_NUMBER() OVER ( \[PARTITION BY partition\_expression] \[ORDER BY order\_expression]) → bigint <a href="#row_number-over--partition-by-partition_expression-order-by-order_expression--bigint" id="row_number-over--partition-by-partition_expression-order-by-order_expression--bigint"></a>

* partition\_expression: An optional expression that groups rows into partitions.
* order\_expression: An optional expression that specifies the order of the rows within each partition.

**Examples**

{% code title="ROW_NUMBER example" %}
```sql
SELECT "Category", 
  "Descript", 
  "DayOfWeek",
  ROW_NUMBER() 
    OVER (
      PARTITION BY "Category" 
      ORDER BY "DayOfWeek")
FROM taxi_trips 

-- Category, Descript, DayOfWeek, EXPR$3
-- ARSON, ARSON, Friday, 1 
-- ARSON, ARSON, Friday, 2
```
{% endcode %}
