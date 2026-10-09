---
description: Returns the smallest value from a list of expressions.
---

# LEAST

### Syntax <a href="#syntax" id="syntax"></a>

#### LEAST(expression) → same as input type <a href="#leastexpression--same-as-input-type" id="leastexpression--same-as-input-type"></a>

* expression: The arguments must include at least one expression. All the expressions should be of the same type or compatible types. Expressions must be of primitive data types.

**Examples**

{% code title="LEAST example" %}
```sql
SELECT LEAST(1, 5, 3, 8)
-- 1
```
{% endcode %}
