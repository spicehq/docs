---
description: Returns the largest value from a list of expressions.
---

# GREATEST

### Syntax <a href="#syntax" id="syntax"></a>

#### GREATEST(expression) → same as input type <a href="#greatestexpression--same-as-input-type" id="greatestexpression--same-as-input-type"></a>

* expression: The arguments must include at least one expression. All the expressions should be of the same type or compatible types. Expressions must be of primitive data types.

**Examples**

{% code title="GREATEST example" %}
```sql
SELECT GREATEST(1, 5, 3, 8)
-- 8
```
{% endcode %}
