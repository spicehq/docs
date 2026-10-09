---
description: Returns the number of months between two date or timestamp values.
---

# MONTHS\_BETWEEN

### Syntax <a href="#syntax" id="syntax"></a>

#### MONTHS\_BETWEEN(_date\_timestamp\_expression1_ string, _date\_timestamp\_expression2_ string) → float <a href="#months_betweendate_timestamp_expression1-string-date_timestamp_expression2-string--float" id="months_betweendate_timestamp_expression1-string-date_timestamp_expression2-string--float"></a>

* date\_timestamp\_expression1: The `DATE` or `TIMESTAMP` to subtract from.
* date\_timestamp\_expression2: The `DATE` or `TIMESTAMP` to subtract.

**Examples**

{% code title="MONTHS_BETWEEN example" %}
```sql
SELECT MONTHS_BETWEEN('2018-11-01 10:30:00', '2019-02-28')
-- -3.870967741935484
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

If date or timestamp `date_timestamp_expression1` represents an earlier point in time than `date_timestamp_expression2`, then `MONTHS_BETWEEN(date_timestamp_expression1, date_timestamp_expression2)` returns a negative value; otherwise it returns a positive value. You can use a date value for one input parameter and a timestamp for the other.
