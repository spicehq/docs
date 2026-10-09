---
description: >-
  Computes the boolean OR of two boolean expressions. Returns TRUE if one or
  both expressions evaluate to TRUE. Returns FALSE if both expressions evaluate
  to FALSE.
---

# BOOL\_OR

### Syntax <a href="#syntax" id="syntax"></a>

#### BOOL\_OR(_bool\_expression1_ boolean, _bool\_expression2_ boolean) → boolean <a href="#bool_orbool_expression1-boolean-bool_expression2-boolean--boolean" id="bool_orbool_expression1-boolean-bool_expression2-boolean--boolean"></a>

* bool\_expression1: Boolean input expression.
* bool\_expression2: Boolean input expression.

**Examples**

{% code title="BOOL_OR example" %}
```sql
SELECT BOOLEANOR(TRUE, FALSE)
-- True
```
{% endcode %}
