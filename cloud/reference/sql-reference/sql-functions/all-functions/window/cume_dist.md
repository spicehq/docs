---
description: >-
  Returns the cumulative distribution of the current row with regard to other
  values within the same window partition.
---

# CUME\_DIST

### Syntax <a href="#syntax" id="syntax"></a>

#### CUME\_DIST() OVER ( \[PARTITION BY partition\_expression] \[ORDER BY order\_expression]) → double <a href="#cume_dist-over--partition-by-partition_expression-order-by-order_expression--double" id="cume_dist-over--partition-by-partition_expression-order-by-order_expression--double"></a>

* partition\_expression: An optional expression that groups rows into partitions.
* order\_expression: An optional expression that specifies the order of the rows within each partition.

**Examples**

{% code title="CUME_DIST example" %}
```sql
SELECT "Category", 
  "Descript", 
  "DayOfWeek",
  CUME_DIST() 
    OVER (
      PARTITION BY "Category" 
      ORDER BY "DayOfWeek")
FROM taxi_trips 

-- Category, Descript, DayOfWeek, EXPR$3
-- ARSON, ARSON, Friday, 0.13636363636363635
-- EMBEZZLEMENT, EMBEZZLED VEHICLE, Friday, 0.18452380952380953
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

This function does not support cumulative frame windows or sliding frame windows.
