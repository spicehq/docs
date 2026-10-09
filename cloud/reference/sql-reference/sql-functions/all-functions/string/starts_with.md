---
description: >-
  Returns whether a string starts with another string. The comparison is
  case-sensitive.
---

# STARTS\_WITH

### Syntax <a href="#syntax" id="syntax"></a>

#### STARTS\_WITH(_expression1_ string, _expression2_ string) → bit <a href="#starts_withexpression1-string-expression2-string--bit" id="starts_withexpression1-string-expression2-string--bit"></a>

* expression1: The input expression to search.
* expression2: The string to search for in the specified expression.

**Examples**

{% code title="STARTS_WITH example" %}
```sql
SELECT IncidntNum, Category, Descript 
FROM  taxi_trips 
  WHERE STARTS_WITH(Category, 'OTHER')
  LIMIT 2

-- IncidntNum, Category, Descript
-- 160003130, OTHER OFFENSES, PAROLE VIOLATION
-- 160073014, OTHER OFFENSES, RESISTING ARREST
```
{% endcode %}
