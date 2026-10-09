---
description: Returns the last day of the month for the specified date or timestamp.
---

# LAST\_DAY

### Syntax <a href="#syntax" id="syntax"></a>

#### LAST\_DAY(_date\_timestamp\_expression_ string) → date <a href="#last_daydate_timestamp_expression-string--date" id="last_daydate_timestamp_expression-string--date"></a>

* date\_timestamp\_expression: A `DATE` or `TIMESTAMP` expression.

**Examples**

{% code title="LAST_DAY example" %}
```sql
SELECT LAST_DAY('2009-01-12 12:58:59')
-- 2009-01-31
```
{% endcode %}

LAST\_DAY example

{% code title="LAST_DAY example" %}
```sql
SELECT pickup_datetime, LAST_DAY(pickup_datetime) AS "last_day" 
FROM taxi_trips 
LIMIT 3

-- pickup_datetime, last_day
-- 2013-05-27 19:15:00.000, 2013-05-31
-- 2013-05-31 16:40:00.000, 2013-05-31
-- 2013-05-27 19:03:00.000, 2013-05-31
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

The return value is always a date regardless of whether `date_timestamp_expression` is a date or a timestamp.
