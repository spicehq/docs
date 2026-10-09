---
description: Calculates Euler’s number, e, raised to the power of the specified value.
---

# EXP

### Syntax <a href="#syntax" id="syntax"></a>

#### EXP(_numeric\_expression_ NUMERIC) → FLOAT <a href="#expnumeric_expression-numeric--float" id="expnumeric_expression-numeric--float"></a>

* numeric\_expression: The exponent value to raise e to. This must be `DOUBLE`, `INTEGER`, or `FLOAT`.

**Examples**

{% code title="EXP example" %}
```sql
SELECT EXP(1)
-- 2.718281828459045
```
{% endcode %}

{% code title="EXP example" %}
```sql
SELECT EXP(10.0)
-- 22026.465794806718
```
{% endcode %}
