---
description: Converts radians to degrees.
---

# DEGREES

### Syntax <a href="#syntax" id="syntax"></a>

#### DEGREES(_numeric\_expression_ NUMERIC) → FLOAT <a href="#degreesnumeric_expression-numeric--float" id="degreesnumeric_expression-numeric--float"></a>

* numeric\_expression: The number of radians. This must be `DOUBLE`, `INTEGER`, or `FLOAT`.

**Examples**

{% code title="DEGREES example" %}
```sql
SELECT DEGREES(PI())
-- 180.0
```
{% endcode %}

{% code title="DEGREES example" %}
```sql
SELECT DEGREES(0)
-- 0.0
```
{% endcode %}

{% code title="DEGREES example" %}
```sql
SELECT DEGREES(1)
-- 57.29577951308232
```
{% endcode %}
