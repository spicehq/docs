---
description: Returns the input expression with all the characters converted to uppercase.
---

# UCASE

### Syntax <a href="#syntax" id="syntax"></a>

#### UCASE(_expression_ varchar) → varchar <a href="#ucaseexpression-varchar--varchar" id="ucaseexpression-varchar--varchar"></a>

* expression: String to convert to uppercase.

**Examples**

{% code title="UCASE example" %}
```sql
SELECT UCASE('a guide to data lakehouses')
-- A GUIDE TO DATA LAKEHOUSES
```
{% endcode %}

{% code title="UCASE example" %}
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

`UCASE` is a synonym for [`UPPER`](upper.md).
