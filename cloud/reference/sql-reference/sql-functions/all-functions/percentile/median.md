---
description: Computes the median of a dataset.
---

# MEDIAN

### Syntax <a href="#syntax" id="syntax"></a>

#### MEDIAN(_order\_by\_expression_ numeric) → double precision <a href="#medianorder_by_expression-numeric--double-precision" id="medianorder_by_expression-numeric--double-precision"></a>

* order\_by\_expression: The expression to sort and compute the median for.

**Examples**

{% code title="MEDIAN example" %}
```sql
SELECT MEDIAN(pop) 
FROM taxi_trips 
-- EXPR$0
-- 2783.0
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

This function is used with only numeric data types in Spice. `NULL` values in the data set are ignored.

This function is equivalent to [`PERCENTILE_CONT(0.5) WITHIN GROUP(ORDER BY order_by_expression)`](percentile\_cont.md).
