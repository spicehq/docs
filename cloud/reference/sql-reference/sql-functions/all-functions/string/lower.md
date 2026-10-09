---
description: Returns the input expression with all the characters converted to lowercase.
---

# LOWER

### Syntax <a href="#syntax" id="syntax"></a>

#### LOWER(_expression_ varchar) → varchar <a href="#lowerexpression-varchar--varchar" id="lowerexpression-varchar--varchar"></a>

* expression: String to convert to lowercase.

**Examples**

{% code title="LOWER example" %}
```sql
SELECT LOWER('A GUIDE to data Lakehouses')
-- a guide to data lakehouses
```
{% endcode %}

{% code title="LOWER example" %}
```sql
SELECT Category, LOWER(Category) 
  FROM taxi_trips 
  LIMIT 3

-- Category, EXPR$1
-- WEAPON LAWS, weapon laws
-- WEAPON LAWS, weapon laws
-- WARRANTS, warrants
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

`LOWER` is a synonym for [`LCASE`](lcase.md).

\
