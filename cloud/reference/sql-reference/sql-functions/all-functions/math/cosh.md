---
description: Computes the hyperbolic cosine of a value in radians.
---

# COSH

### Syntax <a href="#syntax" id="syntax"></a>

#### COSH(_numeric\_expression_ NUMERIC) → FLOAT <a href="#coshnumeric_expression-numeric--float" id="coshnumeric_expression-numeric--float"></a>

* numeric\_expression: The number in radians. This must be `DOUBLE`, `INTEGER`, or `FLOAT`.

**Examples**

{% code title="COSH example" %}
```sql
SELECT COSH(0)
-- 1.0
```
{% endcode %}

{% code title="COSH example" %}
```sql
SELECT COSH(1.0)
-- 1.543080634815244
```
{% endcode %}

{% code title="COSH example" %}
```sql
SELECT COSH(-1)
-- 1.543080634815244
```
{% endcode %}
