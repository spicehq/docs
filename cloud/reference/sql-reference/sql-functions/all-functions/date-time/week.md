---
description: Extracts the week number (from 0 to 53) for a given date or timestamp.
---

# WEEK

### Syntax <a href="#syntax" id="syntax"></a>

#### EXTRACT(WEEK FROM _date\_timestamp\_expression_ string) → bigint <a href="#extractweek-from-date_timestamp_expression-string--bigint" id="extractweek-from-date_timestamp_expression-string--bigint"></a>

* date\_timestamp\_expression: A `DATE` or `TIMESTAMP` expression.

**Examples**

{% code title="WEEK example using a timestamp." %}
```sql
SELECT EXTRACT(WEEK FROM TIMESTAMP '2019-08-12 01:00:00.123456')
-- 33
```
{% endcode %}

{% code title="WEEK example using a date." %}
```sql
SELECT EXTRACT(WEEK FROM DATE '2019-08-12')
-- 33.
```
{% endcode %}

{% code title="WEEK example using the CAST function" %}
```sql
SELECT EXTRACT(WEEK FROM CAST('2019-08-12 01:00:00' AS TIMESTAMP))
-- 33
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

This function uses the [`EXTRACT`](extract.md) function. When using the [`CAST`](../conversion/cast.md) function, timestamps containing milliseconds are not allowed.
