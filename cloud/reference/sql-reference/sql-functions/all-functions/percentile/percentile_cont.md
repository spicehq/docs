---
description: >-
  Computes a percentile value based on a continuous distribution of the column
  input.
---

# PERCENTILE\_CONT

### Syntax <a href="#syntax" id="syntax"></a>

#### PERCENTILE\_CONT(_fraction_ double precision) WITHIN GROUP ( ORDER BY _order\_by\_expression_ numeric \[ ASC | DESC ] ) → double precision <a href="#percentile_contfraction-double-precision-within-group--order-by-order_by_expression-numeric--asc--de" id="percentile_contfraction-double-precision-within-group--order-by-order_by_expression-numeric--asc--de"></a>

* fraction: The fraction/percentile value to compute. The value for this must be a numeric literal in the range of 0 to 1 inclusive and represents a percentage.
* order\_by\_expression: The expression to sort and compute the percentile. You can only provide one expression in the ORDER BY clause. By default, the sort order is ascending (ASC).

**Examples**

{% code title="PERCENTILE_CONT example" %}
```sql
SELECT PERCENTILE_CONT(0.6) WITHIN GROUP ( ORDER BY pop ASC ),
PERCENTILE_CONT(0.6) WITHIN GROUP ( ORDER BY pop DESC )
FROM taxi_trips 

-- EXPR$0, EXPR$0
-- 4519.2, 1806.0
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

This function computes a percentile based on a continuous distribution of the column value. The result is interpolated and may not be equal to any of the actual values in the column. This information was originally provided via [Microsoft’s Transact-SQL Reference Guide](https://bit.ly/3n1NXEz).

This function is used with only numeric data types in Spice. `NULL` values in the data set are ignored. However, passing `NULL` as _fraction_ or as _order\_by\_expression_ will cause an error.
