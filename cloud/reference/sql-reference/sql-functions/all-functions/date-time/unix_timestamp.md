---
description: Returns the Unix epoch time representation of an ISO 8601 timestamp.
---

# UNIX\_TIMESTAMP

### Syntax <a href="#syntax" id="syntax"></a>

#### UNIX\_TIMESTAMP() → int64 <a href="#unix_timestamp--int64" id="unix_timestamp--int64"></a>

**Examples**

{% code title="UNIX_TIMESTAMP example" %}
```sql
SELECT UNIX_TIMESTAMP()
-- 1624928208
```
{% endcode %}

#### UNIX\_TIMESTAMP(_date\_timestamp\_expression_ varchar) → int64 <a href="#unix_timestampdate_timestamp_expression-varchar--int64" id="unix_timestampdate_timestamp_expression-varchar--int64"></a>

* date\_timestamp\_expression: The timestamp to convert to Unix timestamp. The expected format is ‘YYYY-MM-DD HH:MM:SS’ where HH can be a value 1-24.

**Examples**

{% code title="UNIX_TIMESTAMP example" %}
```sql
SELECT UNIX_TIMESTAMP('2021-12-22 13:44:11')
-- 1640180651
```
{% endcode %}

#### UNIX\_TIMESTAMP(_date\_timestamp\_expression_ varchar, _format_ varchar) → int64 <a href="#unix_timestampdate_timestamp_expression-varchar-format-varchar--int64" id="unix_timestampdate_timestamp_expression-varchar-format-varchar--int64"></a>

* date\_timestamp\_expression: The timestamp to convert to Unix timestamp.
* format: Specify the format of the time, date, or timestamp parameter. For example, ‘YY-MM-DD’ or ‘HH:MM:SS’.

**Examples**

{% code title="UNIX_TIMESTAMP example" %}
```sql
SELECT UNIX_TIMESTAMP('21-12-22', 'YY-MM-DD')
-- 1640131200
```
{% endcode %}
