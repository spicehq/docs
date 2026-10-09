---
description: Returns the day of month of the date or timestamp.
---

# DAY

### Syntax <a href="#syntax" id="syntax"></a>

#### DAY(_date\_timestamp\_expression_ string) → bigint <a href="#daydate_timestamp_expression-string--bigint" id="daydate_timestamp_expression-string--bigint"></a>

* date\_timestamp\_expression: A `DATE` or `TIMESTAMP` expression.

**Examples**

{% code title="DAY example" %}
```sql
SELECT "DAY"('2003-02-01 11:43:22')
-- 1
```
{% endcode %}

{% code title="DAY example" %}
```sql
SELECT "Date", "DAY"("Date") 
FROM taxi_trips  
LIMIT 3

-- Date, EXPR$1
-- 2016-01-29, 29
-- 2016-01-29, 29
-- 2016-04-25, 25
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

The function name is a Spice keyword and must be enclosed in double quotes (`"DAY"`). This function is identical to the function [`DAYOFMONTH`](dayofmonth.md).
