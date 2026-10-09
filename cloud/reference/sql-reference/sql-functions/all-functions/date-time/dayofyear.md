---
description: Returns the day of the year (from 1 to 366) of the date or timestamp.
---

# DAYOFYEAR

### Syntax <a href="#syntax" id="syntax"></a>

#### DAYOFYEAR(_date\_timestamp\_expression_ string) → bigint <a href="#dayofyeardate_timestamp_expression-string--bigint" id="dayofyeardate_timestamp_expression-string--bigint"></a>

* date\_timestamp\_expression: A `DATE` or `TIMESTAMP` expression.

**Examples**

{% code title="DAYOFYEAR example" %}
```sql
SELECT DAYOFYEAR(DATE '2021-02-28')
-- 59
```
{% endcode %}

{% code title="DAYOFYEAR example" %}
```sql
SELECT DAYOFYEAR(TIMESTAMP '2021-03-15 11:43:22')
-- 74
```
{% endcode %}
