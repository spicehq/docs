---
description: >-
  Equally splits the rows in each partition into ranked parts specified by the
  integer value and starting from 1. This function requires the ORDER BY clause.
---

# NTILE

### Syntax <a href="#syntax" id="syntax"></a>

#### NTILE(buckets) OVER (PARTITION BY partition\_expression ORDER BY order\_expression) → int <a href="#ntilebuckets-over-partition-by-partition_expression-order-by-order_expression--int" id="ntilebuckets-over-partition-by-partition_expression-order-by-order_expression--int"></a>

* buckets: A positive integer literal.
* partition\_expression: An expression that groups rows into partitions.
* order\_expression: An expression that specifies the order of the rows within each partition.

**Examples**

{% code title="NTILE example" %}
```sql
SELECT "Category", 
  "Descript", 
  "DayOfWeek",
  NTILE(1) 
    OVER (
      PARTITION BY "Category" 
      ORDER BY "DayOfWeek")
FROM taxi_trips 

-- Category, Descript, DayOfWeek, EXPR$3
-- ARSON, ARSON, Friday, 1 
-- EMBEZZLEMENT, EMBEZZLED VEHICLE, Friday, 1
```
{% endcode %}
