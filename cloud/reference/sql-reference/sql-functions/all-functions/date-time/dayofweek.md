---
description: Returns the day of the week (from 1 to 7) of the date or timestamp.
---

# DAYOFWEEK

### Syntax <a href="#syntax" id="syntax"></a>

#### DAYOFWEEK(_date\_timestamp\_expression_ string) → bigint <a href="#dayofweekdate_timestamp_expression-string--bigint" id="dayofweekdate_timestamp_expression-string--bigint"></a>

* date\_timestamp\_expression: A `DATE` or `TIMESTAMP` expression.

**Examples**

{% code title="DAYOFWEEK example" %}
```sql
SELECT DAYOFWEEK(DATE '2021-02-28')
-- 1
```
{% endcode %}

{% code title="DAYOFWEEK example" %}
```sql
SELECT DAYOFWEEK(TIMESTAMP '2021-02-27 11:43:22')
-- 7
```
{% endcode %}
