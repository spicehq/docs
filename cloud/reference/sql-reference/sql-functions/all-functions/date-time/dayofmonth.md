---
description: Returns the day of month of the date or timestamp.
---

# DAYOFMONTH

### Syntax <a href="#syntax" id="syntax"></a>

#### DAYOFMONTH(_date\_timestamp\_expression_ string) → bigint <a href="#dayofmonthdate_timestamp_expression-string--bigint" id="dayofmonthdate_timestamp_expression-string--bigint"></a>

* date\_timestamp\_expression: A `DATE` or `TIMESTAMP` expression.

**Examples**

DAYOFMONTH example

{% code title="DAYOFMONTH example" %}
```sql
SELECT DAYOFMONTH(DATE '2021-02-28')
-- 28
```
{% endcode %}

{% code title="DAYOFMONTH example" %}
```sql
SELECT DAYOFMONTH(TIMESTAMP '2021-02-28 11:43:22')
-- 28
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

This function is identical to the function [`DAY`](day.md).
