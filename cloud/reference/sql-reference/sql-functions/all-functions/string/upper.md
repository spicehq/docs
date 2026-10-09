---
description: Returns the input expression with all the characters converted to uppercase.
---

# UPPER

### Syntax <a href="#syntax" id="syntax"></a>

#### UPPER(_expression_ varchar) → varchar <a href="#upperexpression-varchar--varchar" id="upperexpression-varchar--varchar"></a>

* expression: String to convert to uppercase.

**Examples**

{% code title="UPPER example" %}
```sql
SELECT UPPER('a guide to data lakehouses')
-- A GUIDE TO DATA LAKEHOUSES
```
{% endcode %}

{% code title="UPPER example" %}
```sql
SELECT DayOfWeek, UCASE(DayOfWeek) 
  FROM taxi_trips 
  LIMIT 3

-- DayOfWeek, EXPR$1
-- Friday, FRIDAY
-- Friday, FRIDAY
-- Monday, MONDAY
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

`UPPER` is a synonym for [`UCASE`](ucase.md).
