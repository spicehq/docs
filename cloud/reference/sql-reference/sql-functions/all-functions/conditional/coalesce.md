---
description: >-
  Evaluates the arguments in order and returns the value of the first expression
  that does not contain NULL.
---

# COALESCE

### Syntax <a href="#syntax" id="syntax"></a>

#### COALESCE(expression1, expression2, \[ …, expressionN ]) → same as input type <a href="#coalesceexpression1-expression2---expressionn---same-as-input-type" id="coalesceexpression1-expression2---expressionn---same-as-input-type"></a>

* expression: A combination of symbols and operators that the database evaluates to obtain a single data value. Expressions can be a single constant, variable, column, or scalar function.

**Examples**

{% code title="COALESCE example" %}
```sql
SELECT COALESCE(address1, address2, city, state, zipCode)
FROM customers

-- 123 Main Street
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

All arguments in the `SELECT` statement must have the same data type. Requires a minimum of two arguments, but there is no set maximum limit.

\
