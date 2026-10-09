---
description: >-
  Returns the remainder of the input expression divided by the second input
  expression.
---

# MOD

### Syntax <a href="#syntax" id="syntax"></a>

#### MOD(_numeric\_expression_ int64, _numeric\_expression_ int64) → int64 <a href="#modnumeric_expression-int64-numeric_expression-int64--int64" id="modnumeric_expression-int64-numeric_expression-int64--int64"></a>

* numeric\_expression: The first numeric expression.
* numeric\_expression: The second numeric expression.

**Examples**

{% code title="MOD example" %}
```sql
SELECT MOD(50, 7)
-- 1
```
{% endcode %}

#### MOD(_numeric\_expression_ int64, _numeric\_expression_ int32) → int32 <a href="#modnumeric_expression-int64-numeric_expression-int32--int32" id="modnumeric_expression-int64-numeric_expression-int32--int32"></a>

* numeric\_expression: The first numeric expression.
* numeric\_expression: The second numeric expression.

**Examples**

{% code title="MOD example" %}
```sql
SELECT MOD(35, 5)
-- 0
```
{% endcode %}

#### MOD(_numeric\_expression_ decimal(0,0), _numeric\_expression_ decimal(0,0)) → decimal(0,0) <a href="#modnumeric_expression-decimal00-numeric_expression-decimal00--decimal00" id="modnumeric_expression-decimal00-numeric_expression-decimal00--decimal00"></a>

* numeric\_expression: The first numeric expression.
* numeric\_expression: The second numeric expression.

**Examples**

{% code title="MOD example" %}
```sql
SELECT MOD(47.6, 5.2)
-- 0.8
```
{% endcode %}
