---
description: Computes the cosine of a value in radians.
---

# COS

### Syntax <a href="#syntax" id="syntax"></a>

#### COS(_numeric\_expression_ NUMERIC) → FLOAT <a href="#cosnumeric_expression-numeric--float" id="cosnumeric_expression-numeric--float"></a>

* numeric\_expression: The number in radians. This must be `DOUBLE`, `INTEGER`, or `FLOAT`.

**Examples**

{% code title="COS example" %}
```sql
SELECT COS(0)
-- 1.0
```
{% endcode %}

{% code title="COS example" %}
```sql
SELECT COS(1.0)
-- 0.5403023058681398
```
{% endcode %}

{% code title="COS example" %}
```sql
SELECT COS(-1)
-- 0.5403023058681398
```
{% endcode %}
