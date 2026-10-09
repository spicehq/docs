---
description: Computes the arcsine (inverse sine) of a value in radians.
---

# ASIN

### Syntax <a href="#syntax" id="syntax"></a>

#### ASIN(_numeric\_expression_ NUMERIC) → FLOAT <a href="#asinnumeric_expression-numeric--float" id="asinnumeric_expression-numeric--float"></a>

* numeric\_expression: The number in radians. This must be `DOUBLE`, `INTEGER`, or `FLOAT`.

**Examples**

{% code title="ASIN example" %}
```sql
SELECT ASIN(0)
-- 0.0
```
{% endcode %}

{% code title="ASIN example" %}
```sql
SELECT ASIN(1)
-- 1.5707963267948966
```
{% endcode %}

{% code title="ASIN example" %}
```sql
SELECT ASIN(-1)
-- -1.5707963267948966
```
{% endcode %}
