---
description: Add (or subtract) an interval of time from a date/timestamp value or column.
---

# TIMESTAMPADD

### Syntax <a href="#syntax" id="syntax"></a>

#### TIMESTAMPADD(_unit_ symbol, _count_ integer, _givenTime_ date or timestamp) → date or timestamp <a href="#timestampaddunit-symbol-count-integer-giventime-date-or-timestamp--date-or-timestamp" id="timestampaddunit-symbol-count-integer-giventime-date-or-timestamp--date-or-timestamp"></a>

* unit: The unit of the interval. Must be one of the following: `YEAR`, `QUARTER`, `MONTH`, `WEEK`, `DAY`, `HOUR`, `MINUTE`, `SECOND`.
* count: Number of units to be added (or subtracted) from `givenTime`. To subtract units, pass a negative number.
* givenTime: Value to which to add units (either a database column in `DATE` or `TIMESTAMP` format, or literal value explicitly converted to `DATE` or `TIMESTAMP`).

**Examples**

{% code title="TIMESTAMPADD example" %}
```sql
SELECT TIMESTAMPADD(DAY, 1, DATE '2021-04-01')
-- 2021-04-02
```
{% endcode %}

{% code title="TIMESTAMPADD example" %}
```sql
SELECT TIMESTAMPADD(HOUR, -2, TIMESTAMP '2021-04-01 17:14:32')
-- 2021-04-01 15:14:32
```
{% endcode %}

{% code title="Get TIMESTAMP as of 30 mins ago" %}
```sql
SELECT TIMESTAMPADD(MINUTE, -30, CURRENT_TIMESTAMP)
-- 2023-04-13 16:51:27.467
```
{% endcode %}

{% code title="Get TIMESTAMP in 3 seconds" %}
```sql
SELECT TIMESTAMPADD(SECOND, 3, CURRENT_TIMESTAMP)
-- 2023-04-13 17:24:15.446
```
{% endcode %}

