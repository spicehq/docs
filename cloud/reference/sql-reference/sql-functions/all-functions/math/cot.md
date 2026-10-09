---
description: Computes the cotangent of a value in radians.
---

# COT

### Syntax <a href="#syntax" id="syntax"></a>

#### COT(_numeric\_expression_ NUMERIC) → FLOAT <a href="#cotnumeric_expression-numeric--float" id="cotnumeric_expression-numeric--float"></a>

* numeric\_expression: The number in radians. This must be `DOUBLE`, `INTEGER`, or `FLOAT`.

**Examples**

{% code title="COT example" %}
```sql
SELECT COT(0)
-- 1.0
```
{% endcode %}

{% code title="COT example" %}
```sql
SELECT COT(1.0)
-- 0.5403023058681398
```
{% endcode %}

{% code title="COT example" %}
```sql
SELECT COT(-1)
-- 0.5403023058681398
```
{% endcode %}

\
