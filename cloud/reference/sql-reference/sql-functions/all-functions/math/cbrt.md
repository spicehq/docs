---
description: Computes the cube root of a numeric expression.
---

# CBRT

### Syntax <a href="#syntax" id="syntax"></a>

#### CBRT(_numeric\_expression_ NUMERIC) → FLOAT <a href="#cbrtnumeric_expression-numeric--float" id="cbrtnumeric_expression-numeric--float"></a>

* numeric\_expression: The number (`DOUBLE`, `FLOAT`, `INTEGER`) used to compute the cube root.

**Examples**

{% code title="CBRT example" %}
```sql
SELECT CBRT(8)
-- 2.0
```
{% endcode %}

{% code title="CBRT example" %}
```sql
SELECT CBRT(120)
-- 4.932424148660941
```
{% endcode %}

{% code title="CBRT example" %}
```sql
SELECT CBRT(99.5)
-- 4.633839922986558
```
{% endcode %}
