---
description: Computes the average of a set of values.
---

# AVG

### Syntax <a href="#syntax" id="syntax"></a>

#### AVG(_numeric\_expression_ NUMERIC) → FLOAT <a href="#avgnumeric_expression-numeric--float" id="avgnumeric_expression-numeric--float"></a>

* numeric\_expression: `DOUBLE`, `FLOAT`, `INTEGER`, `INTERVAL_DATE`, `INTERVAL_YEAR`

**Examples**

{% code title="AVG example" %}
```sql
SELECT AVG(3)
-- 3.0
```
{% endcode %}

{% code title="AVG example" %}
```sql
SELECT AVG("val")
-- -0.333333
```
{% endcode %}

\


