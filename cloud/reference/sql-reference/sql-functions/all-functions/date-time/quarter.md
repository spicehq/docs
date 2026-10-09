---
description: Extracts the quarter number (from 1 to 4) for a given date or timestamp.
---

# QUARTER

### Syntax <a href="#syntax" id="syntax"></a>

#### EXTRACT(QUARTER FROM _date\_timestamp\_expression_ string) → bigint <a href="#extractquarter-from-date_timestamp_expression-string--bigint" id="extractquarter-from-date_timestamp_expression-string--bigint"></a>

* date\_timestamp\_expression: A `DATE` or `TIMESTAMP` expression.

**Examples**

{% code title="QUARTER example using a timestamp." %}
```sql
SELECT EXTRACT(QUARTER FROM TIMESTAMP '2019-08-12 01:00:00.123456')
-- 3
```
{% endcode %}

{% code title="QUARTER example using a date." %}
```sql
SELECT EXTRACT(QUARTER FROM DATE '2019-08-12')
-- 3
```
{% endcode %}

{% code title="QUARTER example using the CAST function." %}
```sql
SELECT EXTRACT(QUARTER FROM CAST('2019-08-12 01:00:00' AS TIMESTAMP))
-- 3
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

This function uses the [`EXTRACT`](extract.md) function. When using the [`CAST`](../conversion/cast.md) function, timestamps containing milliseconds are not allowed.
