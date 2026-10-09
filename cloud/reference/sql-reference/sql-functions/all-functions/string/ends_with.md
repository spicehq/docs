---
description: >-
  Returns whether a string ends with another string. The comparison is
  case-sensitive.
---

# ENDS\_WITH

### Syntax <a href="#syntax" id="syntax"></a>

#### ENDS\_WITH(_expression1_ string, _expression2_ string) → bit <a href="#ends_withexpression1-string-expression2-string--bit" id="ends_withexpression1-string-expression2-string--bit"></a>

* expression1: The input expression to search.
* expression2: The string to search for in the specified expression.

**Examples**

{% code title="ENDS_WITH example" %}
```sql
SELECT IncidntNum, Category, Descript 
FROM  taxi_trips 
WHERE ENDS_WITH(Category, 'LAWS')
LIMIT 2

-- IncidntNum, Category, Descript
-- 120058272, WEAPON LAWS, POSS OF PROHIBITED WEAPON
-- 120058272, WEAPON LAWS, FIREARM, LOADED, IN VEHICLE
```
{% endcode %}
