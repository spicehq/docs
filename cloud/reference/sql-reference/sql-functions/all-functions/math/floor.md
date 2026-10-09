---
description: >-
  Returns the value from the specifed expression rounded to the nearest equal or
  smaller integer.
---

# FLOOR

### Syntax <a href="#syntax" id="syntax"></a>

#### FLOOR(_numeric\_expression_ NUMERIC) → INTEGER <a href="#floornumeric_expression-numeric--integer" id="floornumeric_expression-numeric--integer"></a>

* numeric\_expression: The number (`DOUBLE`, `FLOAT`, `INTEGER`) used to compute the floor.

**Examples**

{% code title="FLOOR example" %}
```sql
SELECT FLOOR(0)
-- 0
```
{% endcode %}

{% code title="FLOOR example" %}
```sql
SELECT FLOOR(45.76)
-- 45
```
{% endcode %}

{% code title="FLOOR example" %}
```sql
SELECT FLOOR(-1.3)
-- -2
```
{% endcode %}
