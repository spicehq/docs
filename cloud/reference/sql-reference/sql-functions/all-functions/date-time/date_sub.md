---
description: >-
  Returns the difference of two expressions of time as another expression of
  time.
---

# DATE\_SUB

### Syntax <a href="#syntax" id="syntax"></a>

#### DATE\_SUB(_date\_expression_ STRING, _days_ INTEGER) → DATE <a href="#date_subdate_expression-string-days-integer--date" id="date_subdate_expression-string-days-integer--date"></a>

* date\_expression: A string-formatted date in the format ‘YYYY-MM-DD’.
* days: The number of days to be subtracted from the specified date.

**Examples**

{% code title="Subtracts two days from the specified date." %}
```sql
SELECT DATE_SUB('2022-01-01', 2)
-- 2021-12-30
```
{% endcode %}

{% code title="Subtracts negative two days from the specified date." %}
```sql
SELECT DATE_SUB('2022-01-01', -2)
-- 2022-01-03
```
{% endcode %}

#### DATE\_SUB(_date\_expression_ DATE, _days_ INTEGER) → DATE <a href="#date_subdate_expression-date-days-integer--date" id="date_subdate_expression-date-days-integer--date"></a>

* date\_expression: The date, in the format ‘YYY-MM-DD’, to subtract days from. The value can be either a database column in DATE format, or literal value explicitly converted to DATE.
* days: A 32-bit integer of the number of days to be subtracted from the specified date.

**Examples**

{% code title="Subtracts 30 days from the specified date." %}
```sql
SELECT DATE_SUB(DATE '2022-01-01', 30)
-- 2021-12-02
```
{% endcode %}

Subtracts negative 30 days from the specified date.

{% code title="Subtracts negative 30 days from the specified date." %}
```sql
SELECT DATE_SUB(DATE '2022-01-01', -30)
-- 2022-01-31
```
{% endcode %}

#### DATE\_SUB(_date\_expression_ STRING, _time\_interval_ INTERVAL) → TIMESTAMP <a href="#date_subdate_expression-string-time_interval-interval--timestamp" id="date_subdate_expression-string-time_interval-interval--timestamp"></a>

* date\_expression: A string-formatted date in the format ‘YYYY-MM-DD’.
* time\_interval: A CAST of a number to one of these intervals: DAY, MONTH, YEAR.

**Examples**

{% code title="Subtracts two days from the specified date." %}
```sql
SELECT DATE_SUB('2022-01-01', CAST(2 AS INTERVAL DAY))
-- 2021-12-30 00:00:00
```
{% endcode %}

{% code title="Subtracts negative two days from the specified date." %}
```sql
SELECT DATE_SUB('2022-01-01', CAST(-2 AS INTERVAL DAY))
-- 2022-01-03 00:00:00
```
{% endcode %}

#### DATE\_SUB(_date\_expression_ DATE, _time\_interval_ INTERVAL) → TIMESTAMP <a href="#date_subdate_expression-date-time_interval-interval--timestamp" id="date_subdate_expression-date-time_interval-interval--timestamp"></a>

* date\_expression: The date, in the format ‘YYY-MM-DD’, to subtract the time interval from. The value can be either a database column in DATE format, or literal value explicitly converted to DATE.
* time\_interval: A CAST of a number to one of these intervals: DAY, MONTH, YEAR.

**Examples**

{% code title="DATE_SUB example" %}
```sql
SELECT DATE_SUB(DATE '2022-01-01', CAST(30 AS INTERVAL DAY))
-- 2021-12-02 00:00:00
```
{% endcode %}

#### DATE\_SUB(_timestamp\_expression_ STRING, _time\_interval_ INTERVAL) → TIMESTAMP <a href="#date_subtimestamp_expression-string-time_interval-interval--timestamp" id="date_subtimestamp_expression-string-time_interval-interval--timestamp"></a>

* timestamp\_expression: A string-formatted timestamp in the format ‘YYYY-MM-DD HH24:MI:SS’.
* time\_interval: A CAST of a number to one of these intervals: SECOND, MINUTE, HOUR, DAY, MONTH, YEAR.

**Examples**

{% code title="Subtracts 30 days from the specified timestamp. Note that the time information is lost." %}
```sql
SELECT DATE_SUB('2022-01-01 12:00:00', CAST(30 AS INTERVAL DAY))
-- 2021-12-02 00:00:00
```
{% endcode %}

{% code title="Subtracts negative 30 days from the specified timestamp. Note that the time information is lost." %}
```sql
SELECT DATE_SUB('2022-01-01 12:00:00', CAST(-30 AS INTERVAL DAY))
-- 2022-01-31 00:00:00
```
{% endcode %}

#### DATE\_SUB(_timestamp\_expression_ TIMESTAMP, _time\_interval_ INTERVAL) → TIMESTAMP <a href="#date_subtimestamp_expression-timestamp-time_interval-interval--timestamp" id="date_subtimestamp_expression-timestamp-time_interval-interval--timestamp"></a>

* timestamp\_expression: The timestamp, in the format ‘YYYY-MM-DD HH:MM:SS’, to subtract days from. The value can be either a database column in TIMESTAMP format, or literal value explicitly converted to TIMESTAMP.
* time\_interval: A CAST of a number to one of these intervals: SECOND, MINUTE, HOUR, DAY, MONTH, YEAR.

**Examples**

{% code title="Subtracts 30 days from the specified timestamp." %}
```sql
SELECT DATE_SUB(TIMESTAMP '2022-01-01 12:00:00', CAST(30 AS INTERVAL DAY))
-- 2021-12-02 12:00:00
```
{% endcode %}

{% code title="Subtracts negative 30 days from the specified timestamp." %}
```sql
SELECT DATE_SUB(TIMESTAMP '2022-01-01 12:00:00', CAST(-30 AS INTERVAL DAY))
-- 2022-01-31 12:00:00
```
{% endcode %}

#### DATE\_SUB(_time\_expression_ TIME, _time\_interval_ INTERVAL) → TIME <a href="#date_subtime_expression-time-time_interval-interval--time" id="date_subtime_expression-time-time_interval-interval--time"></a>

* time\_expression: The time, in the format ‘HH:MM:SS’, to subtract the time interval from. The value can be either a database column in TIMESTAMP format, or literal value explicitly converted to TIMESTAMP.
* time\_interval: A CAST of a number to one of these intervals: SECOND, MINUTE, HOUR.

**Examples**

{% code title="Subtracts 30 minutes from the specified time." %}
```sql
SELECT DATE_SUB(TIME '00:00:00', CAST(30 AS INTERVAL MINUTE))
-- 84600
```
{% endcode %}

{% code title="Subtracts negative 30 minutes from the specified time." %}
```sql
SELECT DATE_SUB(TIME '00:00:00', CAST(-30 AS INTERVAL MINUTE))
-- 00:30:00
```
{% endcode %}
