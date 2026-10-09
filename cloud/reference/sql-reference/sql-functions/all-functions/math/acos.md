---
description: Computes the arccosine (inverse cosine) of a value in radians.
---

# ACOS

### Syntax <a href="#syntax" id="syntax"></a>

#### ACOS(_numeric\_expression_ NUMERIC) → FLOAT <a href="#acosnumeric_expression-numeric--float" id="acosnumeric_expression-numeric--float"></a>

* numeric\_expression: The number in radians. This must be `DOUBLE`, `INTEGER`, or `FLOAT`.

**Examples**

{% code title="ACOS example" %}
```sql
SELECT ACOS(0)
-- 1.5707963267948966
```
{% endcode %}

{% code title="ACOS example" %}
```sql
SELECT ACOS(1.0)
-- 0.0
```
{% endcode %}

{% code title="ACOS example" %}
```sql
SELECT ACOS(-1)
-- 3.141592653589793
```
{% endcode %}
