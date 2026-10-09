---
description: Extracts the hour number (from 0 to 23) for a given time or timestamp.
---

# HOUR

### Syntax <a href="#syntax" id="syntax"></a>

#### EXTRACT(HOUR FROM _date\_timestamp\_expression_ string) → bigint <a href="#extracthour-from-date_timestamp_expression-string--bigint" id="extracthour-from-date_timestamp_expression-string--bigint"></a>

* date\_timestamp\_expression: A `TIME`, `TIMESTAMP`, or `DATE` expression.

**Examples**

{% code title="HOUR example using a timestamp." %}
```sql
SELECT EXTRACT(HOUR FROM TIMESTAMP '2019-08-12 01:10:30.123456')
-- 1
```
{% endcode %}

{% code title="HOUR example using a time." %}
```sql
SELECT EXTRACT(HOUR FROM TIME '01:10:30.123456')
-- 1
```
{% endcode %}

{% code title="HOUR example using the CAST function." %}
```sql
SELECT EXTRACT(HOUR FROM CAST('2019-08-12 01:10:30' AS TIMESTAMP))
-- 1
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

This function uses the [`EXTRACT`](extract.md) function. When using the [`CAST`](../conversion/cast.md) function, timestamps containing milliseconds are not allowed.
