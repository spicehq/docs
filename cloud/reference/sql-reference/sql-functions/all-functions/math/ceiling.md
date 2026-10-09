---
description: >-
  Returns the nearest equal or larger value of the input expression. Can also be
  called using CEIL().
---

# CEILING

### Syntax <a href="#syntax" id="syntax"></a>

#### CEILING(_numeric\_expression_ NUMERIC) → INTEGER <a href="#ceilingnumeric_expression-numeric--integer" id="ceilingnumeric_expression-numeric--integer"></a>

* numeric\_expression: The number (`DOUBLE`, `FLOAT`, `INTEGER`) to compute the ceiling.

**Examples**

{% code title="CEILING example" %}
```sql
SELECT CEILING(3.1459)
-- 4
```
{% endcode %}

{% code title="CEILING example" %}
```sql
SELECT CEIL(37.775420706711)
-- 38
```
{% endcode %}

{% code title="CEILING example" %}
```sql
SELECT CEIL(-37.775420706711)
-- -37
```
{% endcode %}

{% code title="CEILING example" %}
```sql
SELECT CEIL(0)
-- 0
```
{% endcode %}
