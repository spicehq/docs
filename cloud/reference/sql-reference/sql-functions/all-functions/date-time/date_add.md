---
description: Returns the sum of two expressions of time as another expression of time.
---

# DATE\_ADD

### Syntax <a href="#syntax" id="syntax"></a>

#### DATE\_ADD(_date\_expression_ string, _days_ integer) → date <a href="#date_adddate_expression-string-days-integer--date" id="date_adddate_expression-string-days-integer--date"></a>

* date\_expression: A string-formatted date in the format ‘YYYY-MM-DD’.
* days: The number of days to be added to the specified date.

**Examples**

{% code title="Adds two days to the specified date." %}
```sql
SELECT DATE_ADD('2022-01-01', 2)
-- 2022-01-03
```
{% endcode %}

{% code title="Adds negative two days to the specified date." %}
```sql
SELECT DATE_ADD('2022-01-01', -2)
-- 2021-12-30
```
{% endcode %}

#### DATE\_ADD(_date\_expression_ date, _days_ integer) → date <a href="#date_adddate_expression-date-days-integer--date" id="date_adddate_expression-date-days-integer--date"></a>

* date\_expression: The date, in the format ‘YYY-MM-DD’, to add days to. The value can be either a database column in DATE format, or literal value explicitly converted to DATE.
* days: A 32-bit integer of the number of days to be added to the specified date.

**Examples**

{% code title="Adds 30 days to the specified date." %}
```sql
SELECT DATE_ADD(DATE '2022-01-01', 30)
-- 2022-01-31
```
{% endcode %}

{% code title="Adds negative 30 days to the specified date." %}
```sql
SELECT DATE_ADD(DATE '2022-01-01', -30)
-- 2021-12-02
```
{% endcode %}

#### DATE\_ADD(_date\_expression_ string, _time\_interval_ interval) → timestamp <a href="#date_adddate_expression-string-time_interval-interval--timestamp" id="date_adddate_expression-string-time_interval-interval--timestamp"></a>

* date\_expression: A string-formatted date in the format ‘YYYY-MM-DD’.
* time\_interval: A CAST of a number to one of these intervals: DAY, MONTH, YEAR.

**Examples**

{% code title="Adds two days to the specified date." %}
```sql
SELECT DATE_ADD('2022-01-01', CAST(2 AS INTERVAL DAY))
-- 2022-01-03 00:00:00
```
{% endcode %}

{% code title="Adds negative two days to the specified date." %}
```sql
SELECT DATE_ADD('2022-01-01', CAST(-2 AS INTERVAL DAY))
-- 2021-12-30 00:00:00
```
{% endcode %}

#### DATE\_ADD(_date\_expression_ date, _time\_interval_ interval) → timestamp <a href="#date_adddate_expression-date-time_interval-interval--timestamp" id="date_adddate_expression-date-time_interval-interval--timestamp"></a>

* date\_expression: The date, in the format ‘YYY-MM-DD’, to add the time interval to. The value can be either a database column in DATE format, or literal value explicitly converted to DATE.
* time\_interval: A CAST of a number to one of these intervals: DAY, MONTH, YEAR.

**Examples**

{% code title="DATE_ADD example" %}
```sql
SELECT DATE_ADD(DATE '2022-01-01', CAST(30 AS INTERVAL DAY))
-- 2022-01-31 00:00:00
```
{% endcode %}

#### DATE\_ADD(_timestamp\_expression_ string, _time\_interval_ interval) → timestamp <a href="#date_addtimestamp_expression-string-time_interval-interval--timestamp" id="date_addtimestamp_expression-string-time_interval-interval--timestamp"></a>

* timestamp\_expression: A string-formatted timestamp in the format ‘YYYY-MM-DD HH24:MI:SS’.
* time\_interval: A CAST of a number to one of these intervals: SECOND, MINUTE, HOUR, DAY, MONTH, YEAR.

**Examples**

{% code title="Adds 30 days to the specified timestamp. Note that the time information is lost." %}
```sql
SELECT DATE_ADD('2022-01-01 12:00:00', CAST(30 AS INTERVAL DAY))
-- 2022-01-31 00:00:00
```
{% endcode %}

{% code title="Adds negative 30 days to the specified timestamp. Note that the time information is lost." %}
```sql
SELECT DATE_ADD('2022-01-01 12:00:00', CAST(-30 AS INTERVAL DAY))
-- 2021-12-02 00:00:00
```
{% endcode %}

#### DATE\_ADD(_timestamp\_expression_ timestamp, _time\_interval_ interval) → timestamp <a href="#date_addtimestamp_expression-timestamp-time_interval-interval--timestamp" id="date_addtimestamp_expression-timestamp-time_interval-interval--timestamp"></a>

* timestamp\_expression: The timestamp, in the format ‘YYYY-MM-DD HH:MM:SS’, to add days to. The value can be either a database column in TIMESTAMP format, or literal value explicitly converted to TIMESTAMP.
* time\_interval: A CAST of a number to one of these intervals: SECOND, MINUTE, HOUR, DAY, MONTH, YEAR.

**Examples**

Adds 30 days to the specified timestamp.

{% code title="Adds 30 days to the specified timestamp." %}
```sql
SELECT DATE_ADD(TIMESTAMP '2022-01-01 12:00:00', CAST(30 AS INTERVAL DAY))
-- 2022-01-31 12:00:00
```
{% endcode %}

{% code title="Adds negative 30 days to the specified timestamp." %}
```sql
SELECT DATE_ADD(TIMESTAMP '2022-01-01 12:00:00', CAST(-30 AS INTERVAL DAY))
-- 2021-12-02 12:00:00
```
{% endcode %}

#### DATE\_ADD(_time\_expression_ time, _time\_interval_ interval) → time <a href="#date_addtime_expression-time-time_interval-interval--time" id="date_addtime_expression-time-time_interval-interval--time"></a>

* time\_expression: The time, in the format ‘HH:MM:SS’, to add the time interval to. The value can be either a database column in TIMESTAMP format, or literal value explicitly converted to TIMESTAMP.
* time\_interval: A CAST of a number to one of these intervals: SECOND, MINUTE, HOUR.

**Examples**

{% code title="Adds 30 minutes to the specified time." %}
```sql
SELECT DATE_ADD(TIME '00:00:00', CAST(30 AS INTERVAL MINUTE))
-- 00:30:00
```
{% endcode %}

{% code title="Adds negative 30 minutes to the specified time." %}
```sql
SELECT DATE_ADD(TIME '00:00:00', CAST(-30 AS INTERVAL MINUTE))
-- 84600
```
{% endcode %}
