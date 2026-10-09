---
description: >-
  Returns the date or timestamp of the first specified day of week that occurs
  after the input date.
---

# NEXT\_DAY

### Syntax <a href="#syntax" id="syntax"></a>

#### NEXT\_DAY(_date\_timestamp\_expression_ string, _day\_of\_week_ string) → date <a href="#next_daydate_timestamp_expression-string-day_of_week-string--date" id="next_daydate_timestamp_expression-string-day_of_week-string--date"></a>

* date\_timestamp\_expression: A `DATE` or `TIMESTAMP` expression.
* day\_of\_week: A string expression identifying a day of the week. The value can be a string literal or an expression that returns a string. The string must be one of the following (case-sensitive):
*
  * `SU`, `SUN`, `SUNDAY`
  * `MO`, `MON`, `MONDAY`
  * `TU`, `TUE`, `TUESDAY`
  * `WE`, `WED`, `WEDNESDAY`
  * `TH`, `THU`, `THURSDAY`
  * `FR`, `FRI`, `FRIDAY`
  * `SA`, `SAT`, `SATURDAY`

**Examples**

{% code title="NEXT_DAY example" %}
```sql
SELECT NEXT_DAY('2015-01-14 12:05:55', 'TU')
-- 2015-01-20
```
{% endcode %}

{% code title="NEXT_DAY example" %}
```sql
SELECT pickup_datetime, NEXT_DAY(pickup_datetime, 'FRIDAY') AS "next_day" 
FROM taxi_trips 
LIMIT 3

-- pickup_datetime, next_day
-- 2013-05-27 19:15:00.000, 2013-05-31
-- 2013-05-31 16:40:00.000, 2013-06-07
-- 2013-05-27 19:03:00.000, 2013-05-31
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

The return value is always a date regardless of whether `date_timestamp_expression` is a date or a timestamp.
