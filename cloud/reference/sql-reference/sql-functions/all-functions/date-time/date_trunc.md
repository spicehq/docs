---
description: Truncates the date or timestamp to the indicated precision.
---

# DATE\_TRUNC

### Syntax <a href="#syntax" id="syntax"></a>

#### DATE\_TRUNC(_time\_unit_ LITERAL, _date\_timestamp\_expression_ DATE OR TIMESTAMP) → DATE <a href="#date_trunctime_unit-literal-date_timestamp_expression-date-or-timestamp--date" id="date_trunctime_unit-literal-date_timestamp_expression-date-or-timestamp--date"></a>

* time\_unit: The time unit that the date or timestamp needs to be truncated at. This must be the literal value of ‘YEAR’, ‘MONTH’, ‘DAY’, ‘HOUR’, ‘MINUTE’, or ‘SECOND’.
* date\_timestamp\_expression: The date or timestamp to truncate. This value must be a literal in the date or timestamp format.

**Examples**

{% code title="DATE_TRUNC example" %}
```sql
SELECT DATE_TRUNC('MONTH', '2021-12-24')
-- 2021-12-01
```
{% endcode %}

{% code title="DATE_TRUNC example" %}
```sql
SELECT DATE_TRUNC('MINUTE', CAST('2021-12-24 12:28:33' as TIMESTAMP))
-- 2021-12-24 12:28:00
```
{% endcode %}

{% code title="DATE_TRUNC example" %}
```sql
SELECT DATE_TRUNC('HOUR', '2021-12-24 12:28:33')
-- 2021-12-24
```
{% endcode %}
