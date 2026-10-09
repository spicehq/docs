---
description: Computes a specific percentile for sorted values in a column.
---

# PERCENTILE\_DISC

### Syntax <a href="#syntax" id="syntax"></a>

#### PERCENTILE\_DISC(_fraction_ double precision) WITHIN GROUP ( ORDER BY _order\_by\_expression_ \[ ASC | DESC ] ) → double precision <a href="#percentile_discfraction-double-precision-within-group--order-by-order_by_expression--asc--desc----do" id="percentile_discfraction-double-precision-within-group--order-by-order_by_expression--asc--desc----do"></a>

* fraction: The fraction/percentile value to compute. The value for this must be a numeric literal in the range of 0 to 1 inclusive and represents a percentage.
* order\_by\_expression: The expression to sort and compute the percentile. You can only provide one expression in the ORDER BY clause.

**Examples**

{% code title="PERCENTILE_DISC example" %}
```sql
SELECT PERCENTILE_DISC(0.6) WITHIN GROUP ( ORDER BY pop ASC ), 
PERCENTILE_DISC(0.6) WITHIN GROUP ( ORDER BY pop DESC )
FROM taxi_trips 

-- EXPR$0, EXPR$0
-- 4520.0, 1806.0
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

This function computes a specific percentile for sorted values in a column. For each percentile value, `PERCENTILE_DISC` sorts the values using the `ORDER BY` clause. The function then returns the value with the smallest [`CUME_DIST`](../window/cume\_dist.md) value given that is greater or equal to the percentile value. For example, `PERCENTILE_DISC (0.5)` computes the 50th percentile (the median) of an expression. The result is equal to a specific column value. This information was originally provided via [Microsoft’s Transact-SQL Reference Guide](https://bit.ly/3BHYHvM).

This function is used with only numeric data types in Spice. `NULL` values in the data set are ignored. However, passing `NULL` as _fraction_ or as _order\_by\_expression_ will cause an error.
