---
description: Returns the week of year of the date or timestamp.
---

# WEEKOFYEAR

### Syntax <a href="#syntax" id="syntax"></a>

#### WEEKOFYEAR(_date\_timestamp\_expression_ string) → bigint <a href="#weekofyeardate_timestamp_expression-string--bigint" id="weekofyeardate_timestamp_expression-string--bigint"></a>

* date\_timestamp\_expression: A `DATE` or `TIMESTAMP` expression.

**Examples**

{% code title="WEEKOFYEAR example" %}
```sql
SELECT WEEKOFYEAR('2003-02-01 11:43:22')
-- 5
```
{% endcode %}

{% code title="WEEKOFYEAR example" %}
```sql
SELECT "Date", WEEKOFYEAR("Date") 
FROM taxi_trips 
LIMIT 3

-- Date, EXPR$1
-- 2016-01-29, 4
-- 2016-01-29, 4
-- 2016-04-25, 17
```
{% endcode %}
