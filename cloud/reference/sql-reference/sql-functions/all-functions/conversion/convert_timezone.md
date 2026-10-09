---
description: Convert timestamp to the specified timezone.
---

# CONVERT\_TIMEZONE

### Syntax <a href="#syntax" id="syntax"></a>

#### CONVERT\_TIMEZONE(_sourceTimezone_ string, _destinationTimezone_ string, _timestamp_ date, timestamp, or string in ISO 8601 format) → timestamp <a href="#convert_timezonesourcetimezone-string-destinationtimezone-string-timestamp-date-timestamp-or-string" id="convert_timezonesourcetimezone-string-destinationtimezone-string-timestamp-date-timestamp-or-string"></a>

* sourceTimezone: The time zone of the timestamp. If you omit this parameter, Spice assumes that the source time zone is UTC.
* destinationTimezone: The time zone to convert the timestamp to.
* timestamp: The timestamp to convert.

**Examples**

{% code title="CONVERT_TIMEZONE example" %}
```sql
select convert_timezone('America/Los_Angeles', 'America/New_York', '2021-04-01 15:27:32')
-- 2021-04-01 18:27:32
```
{% endcode %}

{% code title="CONVERT_TIMEZONE example" %}
```sql
select convert_timezone('America/Los_Angeles', 'America/New_York', timestamp '2021-04-01 15:27:32');
-- 2021-04-01 18:27:32
```
{% endcode %}

{% code title="CONVERT_TIMEZONE example" %}
```sql
select convert_timezone('PST', 'EST', '2021-04-01 15:27:32')
-- 2021-04-01 18:27:32
```
{% endcode %}

{% code title="CONVERT_TIMEZONE example" %}
```sql
select convert_timezone('America/Los_Angeles', 'America/New_York', '2021-04-01')
-- 2021-04-01 03:00:00
```
{% endcode %}

{% code title="CONVERT_TIMEZONE example" %}
```sql
select convert_timezone('America/Los_Angeles', 'America/New_York', date '2021-04-01')
-- 2021-04-01 03:00:00
```
{% endcode %}

{% code title="CONVERT_TIMEZONE example" %}
```sql
select convert_timezone('EDT', '2021-04-01 15:27:32')
-- 2021-04-01 11:27:32
```
{% endcode %}

{% code title="CONVERT_TIMEZONE example" %}
```sql
select convert_timezone('PST', '+02:00', '2021-04-01 15:27:32')
-- 2021-04-02 01:27:32
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

* The `sourceTimezone` and `destinationTimezone` parameters may be `timezone_name` from `sys.timezone_names`, `timezone_abbrev` from `sys.timezone_abbrevs`, or a UTC time offset such as “+02:00”.
* If you specify a time zone name instead of time offset, you may get a different answer depending on when you call this function due to daylight saving time.
