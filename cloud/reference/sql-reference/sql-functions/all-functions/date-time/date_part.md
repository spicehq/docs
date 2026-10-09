---
description: Return subfields such as year or hour from date or timestamp values.
---

# DATE\_PART

### Syntax <a href="#syntax" id="syntax"></a>

#### DATE\_PART(_field_ string, _source_ date or timestamp) → integer <a href="#date_partfield-string-source-date-or-timestamp--integer" id="date_partfield-string-source-date-or-timestamp--integer"></a>

* field: Must be one of the following: YEAR, QUARTER, MONTH, WEEK, DAY, HOUR, MINUTE, SECOND.
* source: The value from which to extract the subfield.

**Examples**

{% code title="DATE_PART example" %}
```sql
SELECT DATE_PART('year', TIMESTAMP '2021-04-01 15:27:32')
-- 2021
```
{% endcode %}

{% code title="DATE_PART example" %}
```sql
SELECT DATE_PART('month', DATE '2021-04-01')
-- 4
```
{% endcode %}
