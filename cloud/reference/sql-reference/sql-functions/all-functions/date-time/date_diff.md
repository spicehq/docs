---
description: >-
  Returns the difference between two expressions of time as another expression
  of time.
---

# DATE\_DIFF

### Syntax <a href="#syntax" id="syntax"></a>

#### DATE\_DIFF(_date\_expression_ DATE, _days_ INTEGER) → DATE <a href="#date_diffdate_expression-date-days-integer--date" id="date_diffdate_expression-date-days-integer--date"></a>

* date\_expression: The date, in the format ‘YYY-MM-DD’, to subtract days from. The value can be either a database column in DATE format, or literal value explicitly converted to DATE.
* days: A 32-bit integer of the number of days to be subtracted from the specified date.

**Examples**

{% code title="Subtracts five days from the specified date." %}
```sql
SELECT DATE_DIFF(DATE '2022-01-01', 5)
-- 2021-12-27
```
{% endcode %}

{% code title="Subtracts negative five days from the specified date." %}
```sql
SELECT DATE_DIFF(DATE '2022-01-01', -5)
-- 2022-01-06
```
{% endcode %}

#### DATE\_DIFF(_date\_expression_ DATE, _date\_expression_ DATE) → INTERVAL DAY <a href="#date_diffdate_expression-date-date_expression-date--interval-day" id="date_diffdate_expression-date-date_expression-date--interval-day"></a>

* date\_expression: The date, in the format ‘YYY-MM-DD’, to subtract the second date from. The value can be either a database column in DATE format, or literal value explicitly converted to DATE.
* date\_expression: The date, in the format ‘YYY-MM-DD’, to subtract from the first date. The value can be either a database column in DATE format, or literal value explicitly converted to DATE.

**Examples**

{% code title="Subtracts the second date from the first date." %}
```sql
SELECT DATE_DIFF(DATE '2022-04-01', DATE '2022-01-01')
-- +090 00:00:00.000
```
{% endcode %}

{% code title="Subtracts the second date from the first date." %}
```sql
SELECT DATE_DIFF(DATE '2022-01-01', DATE '2022-04-01')
-- -090 00:00:00.000
```
{% endcode %}

#### DATE\_DIFF(_timestamp\_expression_ TIMESTAMP, _timestamp\_expression_ TIMESTAMP) → INTERVAL DAY <a href="#date_difftimestamp_expression-timestamp-timestamp_expression-timestamp--interval-day" id="date_difftimestamp_expression-timestamp-timestamp_expression-timestamp--interval-day"></a>

* timestamp\_expression: The timestamp, in the format ‘YYYY-MM-DD HH:MM:SS’, to subtract the second timestamp from. The value can be either a database column in TIMESTAMP format, or literal value explicitly converted to TIMESTAMP.
* timestamp\_expression: The timestamp, in the format ‘YYYY-MM-DD HH:MM:SS’, to subtract from the first timestamp. The value can be either a database column in TIMESTAMP format, or literal value explicitly converted to TIMESTAMP.

**Examples**

{% code title="Subtracts the second timestamp from the first timestamp." %}
```sql
SELECT DATE_DIFF(TIMESTAMP '2022-04-01 12:35:23', TIMESTAMP '2022-01-01 01:00:00')
-- +090 11:35:23.000
```
{% endcode %}

{% code title="Subtracts the second timestamp from the first timestamp." %}
```sql
SELECT DATE_DIFF(TIMESTAMP '2022-01-01 01:00:00', TIMESTAMP '2022-04-01 12:35:23')
-- -090 11:35:23.000
```
{% endcode %}

#### DATE\_DIFF(_time\_expression_ TIME, _time\_interval_ INTERVAL) → TIME <a href="#date_difftime_expression-time-time_interval-interval--time" id="date_difftime_expression-time-time_interval-interval--time"></a>

* time\_expression: The time, in the format ‘HH:MM:SS’, from which to subtract a number of seconds, minutes, or hours. The value can be either a database column in TIME format, or literal value explicitly converted to TIME.
* time\_interval: A CAST of a number to one of these intervals: SECOND, MINUTE, HOUR.

**Examples**

{% code title="Subtracts 30 seconds from the specified time." %}
```sql
SELECT DATE_DIFF(TIME '12:00:00', CAST(30 AS INTERVAL SECOND))
-- 11:59:30
```
{% endcode %}

{% code title="Subtracts 30 minutes from the specified time." %}
```sql
SELECT DATE_DIFF(TIME '12:00:00', CAST(30 AS INTERVAL MINUTE))
-- 11:30:00
```
{% endcode %}

#### DATE\_DIFF(_date\_expression_ DATE, _time\_interval_ INTERVAL) → TIMESTAMP <a href="#date_diffdate_expression-date-time_interval-interval--timestamp" id="date_diffdate_expression-date-time_interval-interval--timestamp"></a>

* date\_expression: The date, in the format ‘YYYY-MM-DD’, from which to subtract a number of days, months, or years. The value can be either a database column in DATE format, or literal value explicitly converted to DATE.
* time\_interval: A CAST of a number to one of these intervals: DAY, MONTH, YEAR.

**Examples**

{% code title="Subtracts 30 days from the specified date." %}
```sql
SELECT DATE_DIFF(DATE '2022-01-01', CAST(30 AS INTERVAL DAY))
-- 2021-12-02 00:00:00
```
{% endcode %}

{% code title="Subtracts 30 months from the specified date." %}
```sql
SELECT DATE_DIFF(DATE '2022-01-01', CAST(30 AS INTERVAL MONTH))
-- 2019-07-01 00:00:00
```
{% endcode %}

#### DATE\_DIFF(_timestamp\_expression_ TIMESTAMP, _days_ INTEGER) → TIMESTAMP <a href="#date_difftimestamp_expression-timestamp-days-integer--timestamp" id="date_difftimestamp_expression-timestamp-days-integer--timestamp"></a>

* timestamp\_expression: The timestamp, in the format ‘YYYY-MM-DD HH:MM:SS’, to subtract days from. The value can be either a database column in TIMESTAMP format, or literal value explicitly converted to TIMESTAMP.
* days: A 32-bit integer of the number of days to be subtracted from the specified timestamp.

**Examples**

{% code title="Subtracts five days from the specified timestamp." %}
```sql
SELECT DATE_DIFF(TIMESTAMP '2022-01-01 12:35:23', 5)
-- 2021-12-27 12:35:23
```
{% endcode %}

{% code title="Subtracts negative five days from the specified timestamp." %}
```sql
SELECT DATE_DIFF(TIMESTAMP '2022-01-01 12:35:23', -5)
-- 2022-01-06 12:35:23
```
{% endcode %}

#### DATE\_DIFF(_timestamp\_expression_ TIMESTAMP, _time\_interval_ INTERVAL) → TIMESTAMP <a href="#date_difftimestamp_expression-timestamp-time_interval-interval--timestamp" id="date_difftimestamp_expression-timestamp-time_interval-interval--timestamp"></a>

* timestamp\_expression: The timestamp, in the format ‘YYYY-MM-DD HH:MM:SS’, from which to subtract a number of seconds, minutes, hours, days, months, or years. The value can be either a database column in TIMESTAMP format, or literal value explicitly converted to TIMESTAMP.
* time\_interval: A CAST of a number to one of these intervals: SECOND, MINUTE, HOUR, DAY, MONTH, YEAR.

**Examples**

{% code title="Subtracts 30 seconds from the specified timestamp." %}
```sql
SELECT DATE_DIFF(TIMESTAMP '2022-01-01 01:00:00.000', CAST(30 AS INTERVAL SECOND))
-- 2022-01-01 00:59:30
```
{% endcode %}

{% code title="Subtracts 30 minutes from the specified timestamp." %}
```sql
SELECT DATE_DIFF(TIMESTAMP '2022-01-01 01:00:00.000', CAST(30 AS INTERVAL MINUTE))
-- 2022-01-01 00:30:00
```
{% endcode %}
