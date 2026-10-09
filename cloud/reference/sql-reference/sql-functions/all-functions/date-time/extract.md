---
description: Extracts the specified date or time part from the date or timestamp.
---

# EXTRACT

### Syntax <a href="#syntax" id="syntax"></a>

#### EXTRACT(_time\_unit_ KEYWORD, _date\_time\_expression_ DATE, TIME, TIMESTAMP) → INTEGER <a href="#extracttime_unit-keyword-date_time_expression-date-time-timestamp--integer" id="extracttime_unit-keyword-date_time_expression-date-time-timestamp--integer"></a>

* time\_unit: The time unit that needs to be extracted from the date, time or timestamp. This must YEAR, MONTH, DAY, HOUR, MINUTE, or SECOND.
* date\_time\_expression: The date, time or timestamp to extract time part from.

**Examples**

{% code title="EXTRACT example" %}
```sql
SELECT EXTRACT(HOUR FROM CAST('05:33:44' AS TIME))
-- 5
```
{% endcode %}

{% code title="EXTRACT example" %}
```sql
SELECT EXTRACT(MONTH FROM CAST('2021-03-22 05:33:44.2' AS TIMESTAMP))
-- 3
```
{% endcode %}

{% code title="EXTRACT example" %}
```sql
SELECT EXTRACT(SECOND FROM CAST('2021-03-22 05:33:44.2' AS TIMESTAMP))
-- 44
```
{% endcode %}

{% code title="EXTRACT example" %}
```sql
SELECT EXTRACT(YEAR FROM CAST('2021-03-22' AS DATE))
-- 2021
```
{% endcode %}
