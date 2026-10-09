---
description: >-
  Returns a result that can be used as a properly escaped data value in a SQL
  statement.
---

# QUOTE

### Syntax <a href="#syntax" id="syntax"></a>

#### QUOTE(_expression_ string) → string <a href="#quoteexpression-string--string" id="quoteexpression-string--string"></a>

* expression: The input string.

**Examples**

{% code title="QUOTE example" %}
```sql
SELECT QUOTE('Spice')
-- 'Spice'
```
{% endcode %}

{% code title="QUOTE example using escaping single quotes." %}
```sql
SELECT QUOTE('Sonar''and''Arctic') 
AS Escaped_String

-- 'Sonar\'and\'Arctic'
```
{% endcode %}

{% code title="QUOTE example that quotes from a column in sample data." %}
```sql
SELECT *, QUOTE(gas_used) 
FROM taxi_trips 
LIMIT 3

-- pickup_datetime, transaction_count, trip_distance_mi, gas_used, base_fee_per_gas, total_amount, EXPR$6
-- 2013-05-27 19:15:00.000, 1, 1.26, 7.5, 0.0, 8.0, '8.0'
-- 2013-05-31 16:40:00.000, 1, 0.73, 5.0, 1.2, 7.7, '7.7'
-- 2013-05-27 19:03:00.000, 2, 9.23, 27.5, 5.0, 38.33, '38.33'
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

The string is returned enclosed by single quotation marks and with each instance of backslash (\\), single quote (‘), ASCII NUL, and `Control+Z` preceded by a backslash (\\). If the argument is `NULL`, the return value is the word `NULL` without enclosing single quotation marks.
