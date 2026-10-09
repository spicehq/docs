---
description: Extracts the month number (from 1 to 12) for a given date or timestamp.
---

# MONTH

### Syntax <a href="#syntax" id="syntax"></a>

#### EXTRACT(MONTH FROM _date\_timestamp\_expression_ string) → bigint <a href="#extractmonth-from-date_timestamp_expression-string--bigint" id="extractmonth-from-date_timestamp_expression-string--bigint"></a>

* date\_timestamp\_expression: A `DATE` or `TIMESTAMP` expression.

**Examples**

{% code title="MONTH example using a timestamp." %}
```sql
SELECT EXTRACT(MONTH FROM TIMESTAMP '2019-08-12 01:00:00.123456')
-- 8
```
{% endcode %}

{% code title="MONTH example using a date." %}
```sql
SELECT EXTRACT(MONTH FROM DATE '2019-08-12')
-- 8
```
{% endcode %}

{% code title="MONTH example using the CAST function." %}
```sql
SELECT EXTRACT(MONTH FROM CAST('2019-08-12 01:00:00' AS TIMESTAMP))
-- 8
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

This function uses the [`EXTRACT`](extract.md) function. When using the [`CAST`](../conversion/cast.md) function, timestamps containing milliseconds are not allowed.
