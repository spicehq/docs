---
description: Extracts the minute number (from 0 to 59) for a given time or timestamp.
---

# MINUTE

### Syntax <a href="#syntax" id="syntax"></a>

#### EXTRACT(MINUTE FROM _date\_timestamp\_expression_ string) → bigint <a href="#extractminute-from--date_timestamp_expression-string--bigint" id="extractminute-from--date_timestamp_expression-string--bigint"></a>

* timestamp\_expression: A `TIME`, `TIMESTAMP`, or `DATE` expression.

**Examples**

{% code title="MINUTE example using a timestamp." %}
```sql
SELECT EXTRACT(MINUTE FROM TIMESTAMP '2019-08-12 01:10:30.123456')
-- 10
```
{% endcode %}

{% code title="MINUTE example using a time." %}
```sql
SELECT EXTRACT(MINUTE FROM TIME '01:10:30.123456')
-- 10
```
{% endcode %}

{% code title="MINUTE example using the CAST function." %}
```sql
SELECT EXTRACT(MINUTE FROM CAST('2019-08-12 01:10:30' AS TIMESTAMP))
-- 10
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

This function uses the [`EXTRACT`](extract.md) function. When using the [`CAST`](../conversion/cast.md) function, timestamps containing milliseconds are not allowed.
