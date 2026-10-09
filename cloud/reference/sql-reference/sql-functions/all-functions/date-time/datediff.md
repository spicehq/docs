---
description: Compares two dates or timestamps and returns the difference in days.
---

# DATEDIFF

### Syntax <a href="#syntax" id="syntax"></a>

#### DATEDIFF(_endDate_ string, _startDate_ string) → integer <a href="#datediffenddate-string-startdate-string--integer" id="datediffenddate-string-startdate-string--integer"></a>

* endDate: A `DATE` or `TIMESTAMP` expression.
* startDate: A `DATE` or `TIMESTAMP` expression.

**Examples**

{% code title="DATEDIFF example" %}
```sql
SELECT DATEDIFF('2021-02-28', '2021-01-01')
-- 58
```
{% endcode %}

{% code title="DATEDIFF example" %}
```sql
SELECT DATEDIFF('2005-04-09 12:05:55', '2003-02-01 11:43:22')
-- 798
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

If `endDate` is before `startDate` the result is negative.
