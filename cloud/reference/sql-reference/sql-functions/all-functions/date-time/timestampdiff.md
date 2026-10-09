---
description: Return the amount of time between two date or timestamp values.
---

# TIMESTAMPDIFF

### Syntax <a href="#syntax" id="syntax"></a>

#### TIMESTAMPDIFF(_unit_ symbol, _giventime1_ date or timestamp, _givenTime2_ date or timestamp) → integer <a href="#timestampdiffunit-symbol-giventime1-date-or-timestamp-giventime2-date-or-timestamp--integer" id="timestampdiffunit-symbol-giventime1-date-or-timestamp-giventime2-date-or-timestamp--integer"></a>

* unit: The unit of the interval. Must be one of the following: `YEAR`, `QUARTER`, `MONTH`, `WEEK`, `DAY`, `HOUR`, `MINUTE`, `SECOND`.
* giventime1: The first `DATE` or `TIMESTAMP` (subtrahend).
* givenTime2: The second `DATE` or `TIMESTAMP` (minuend).

**Examples**

TIMESTAMPDIFF example

{% code title="TIMESTAMPDIFF example" %}
```sql
SELECT TIMESTAMPDIFF(MONTH, DATE '2021-02-01', DATE '2021-05-01');
-- 3
```
{% endcode %}

TIMESTAMPDIFF example

{% code title="TIMESTAMPDIFF example" %}
```sql
SELECT TIMESTAMPDIFF(DAY, TIMESTAMP '2003-02-01 11:43:22', TIMESTAMP '2005-04-09 12:05:55');
-- 798
```
{% endcode %}
