---
description: >-
  Compares two expressions. If the values in each expression are equal, returns
  NULL and, if they are not equal, returns the value of the first expression.
---

# NULLIF

### Syntax <a href="#syntax" id="syntax"></a>

#### NULLIF(expression1, expression2) → same as input type <a href="#nullifexpression1-expression2--same-as-input-type" id="nullifexpression1-expression2--same-as-input-type"></a>

* expression: The expressions can be any data type, however all the expressions contained in the argument must be of the same type.

**Examples**

{% code title="NULLIF example" %}
```sql
SELECT NULLIF(user_id, customer_id)

-- user_id
```
{% endcode %}
