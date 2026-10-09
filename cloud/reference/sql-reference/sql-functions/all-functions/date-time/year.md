---
description: Extracts the year for a given date or timestamp.
---

# YEAR

### Syntax <a href="#syntax" id="syntax"></a>

#### EXTRACT(YEAR FROM _date\_timestamp\_expression_ string) → bigint <a href="#extractyear-from-date_timestamp_expression-string--bigint" id="extractyear-from-date_timestamp_expression-string--bigint"></a>

* date\_timestamp\_expression: A `DATE` or `TIMESTAMP` expression.

**Examples**

{% code title="YEAR example using a timestamp." %}
```sql
SELECT EXTRACT(YEAR FROM TIMESTAMP '2019-08-12 01:00:00.123456')
-- 2019
```
{% endcode %}

{% code title="YEAR example using a date." %}
```sql
SELECT EXTRACT(YEAR FROM DATE '2019-08-12')
-- 2019
```
{% endcode %}

{% code title="YEAR example using the CAST function." %}
```sql
SELECT EXTRACT(YEAR FROM CAST('2019-08-12 01:00:00' AS TIMESTAMP))
-- 2019
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

This function uses the [`EXTRACT`](extract.md) function. When using the [`CAST`](../conversion/cast.md) function, timestamps containing milliseconds are not allowed.
