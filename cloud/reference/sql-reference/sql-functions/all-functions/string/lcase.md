---
description: Returns the input expression with all the characters converted to lowercase.
---

# LCASE

### Syntax <a href="#syntax" id="syntax"></a>

#### LCASE(_expression_ varchar) → varchar <a href="#lcaseexpression-varchar--varchar" id="lcaseexpression-varchar--varchar"></a>

* expression: String to convert to lowercase.

**Examples**

{% code title="LCASE example" %}
```sql
SELECT LCASE('A GUIDE to data Lakehouses')
-- a guide to data lakehouses
```
{% endcode %}

{% code title="LCASE example" %}
```sql
SELECT Category, LCASE(Category) 
  FROM taxi_trips 
  LIMIT 3

-- Category, EXPR$1
-- WEAPON LAWS, weapon laws
-- WEAPON LAWS, weapon laws
-- WARRANTS, warrants
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

`LCASE` is a synonym for [`LOWER`](lower.md).
