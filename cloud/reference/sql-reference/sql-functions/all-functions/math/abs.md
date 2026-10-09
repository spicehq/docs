---
description: Computes the absolute value of a numeric expression.
---

# ABS

### Syntax <a href="#syntax" id="syntax"></a>

#### ABS(_numeric\_expression_ NUMERIC) → NUMERIC <a href="#absnumeric_expression-numeric--numeric" id="absnumeric_expression-numeric--numeric"></a>

* numeric\_expression: `BINARY`, `DECIMAL`, `DOUBLE`, `FLOAT`, `INTEGER`

**Examples**

{% code title="ABS example" %}
```sql
SELECT ABS(0.0)
-- 0.0
```
{% endcode %}

{% code title="ABS example" %}
```sql
SELECT ABS(-2)
-- 2
```
{% endcode %}

{% code title="ABS example" %}
```sql
SELECT ABS(NULL)
-- None
```
{% endcode %}
