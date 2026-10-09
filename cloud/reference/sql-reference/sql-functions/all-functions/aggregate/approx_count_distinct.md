---
description: Returns the approximate number of unique, non-null values in a column.
---

# APPROX\_COUNT\_DISTINCT

### Syntax <a href="#syntax" id="syntax"></a>

#### APPROX\_COUNT\_DISTINCT(_column\_name_ any primitive) → BIGINT <a href="#approx_count_distinctcolumn_name-any-primitive--bigint" id="approx_count_distinctcolumn_name-any-primitive--bigint"></a>

* `column_name`: You can specify a column of any primitive data type.

**Examples**

{% code title="APPROX_COUNT_DISTINCT example" %}
```sql
SELECT APPROX_COUNT_DISTINCT(number)
FROM taxi_trips
-- 143

```
{% endcode %}
