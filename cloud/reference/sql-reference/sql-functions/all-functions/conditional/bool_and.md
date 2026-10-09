---
description: >-
  Computes the boolean AND of two boolean expressions. Returns TRUE if both
  expressions evaluate to TRUE. Returns FALSE if one or both expression(s)
  evaluate(s) to FALSE.
---

# BOOL\_AND

### Syntax <a href="#syntax" id="syntax"></a>

#### BOOL\_AND(_bool\_expression1_ boolean, _bool\_expression2_ boolean) → boolean <a href="#bool_andbool_expression1-boolean-bool_expression2-boolean--boolean" id="bool_andbool_expression1-boolean-bool_expression2-boolean--boolean"></a>

* bool\_expression1: Boolean input expression.
* bool\_expression2: Boolean input expression.

**Examples**

{% code title="BOOL_AND example" %}
```sql
SELECT BOOLEANAND(TRUE, FALSE)
-- False
```
{% endcode %}
