---
description: Extracts the second number (from 0 to 59) for a given date or timestamp.
---

# SECOND

### Syntax <a href="#syntax" id="syntax"></a>

#### EXTRACT(SECOND FROM _date\_timestamp\_expression_ string) → bigint <a href="#extractsecond-from-date_timestamp_expression-string--bigint" id="extractsecond-from-date_timestamp_expression-string--bigint"></a>

* timestamp\_expression: A `TIME`, `TIMESTAMP`, or `DATE` expression.

**Examples**

{% code title="SECOND example using a timestamp." %}
```sql
SELECT EXTRACT(SECOND FROM TIMESTAMP '2019-08-12 01:10:30.123456')
-- 1
```
{% endcode %}

{% code title="SECOND example using a time." %}
```sql
SELECT EXTRACT(SECOND FROM TIME '01:10:30.123456')
-- 1
```
{% endcode %}

{% code title="SECOND example using the CAST function" %}
```sql
SELECT EXTRACT(SECOND FROM CAST('2019-08-12 01:10:30' AS TIMESTAMP))
-- 1
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

This function uses the [`EXTRACT`](extract.md) function. When using the [`CAST`](../conversion/cast.md) function, timestamps containing milliseconds are not allowed.
