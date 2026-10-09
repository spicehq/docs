---
description: Returns the result of raising the input value to the specified power.
---

# POWER

### Syntax <a href="#syntax" id="syntax"></a>

#### POWER(_numeric\_expression_ double, _power_ double) → double <a href="#powernumeric_expression-double-power-double--double" id="powernumeric_expression-double-power-double--double"></a>

* numeric\_expression: The input expression.
* power: The power to raise the numeric\_expression to.

**Examples**

{% code title="POWER example" %}
```sql
SELECT POWER(5, 2)
-- 25.0
```
{% endcode %}

{% code title="POWER example" %}
```sql
SELECT POWER(0.1, 2)
-- 0.010000000000000002
```
{% endcode %}

{% code title="POWER example" %}
```sql
SELECT POWER(-2, 2)
-- 4.0
```
{% endcode %}

{% code title="POWER example" %}
```sql
SELECT POWER(10, -2)
-- 0.01
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

This function always returns a `DOUBLE` even if the parameters are both integers.
