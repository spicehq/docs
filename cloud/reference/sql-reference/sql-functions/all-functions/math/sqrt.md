---
description: Returns the square root of the non-negative numeric expression.
---

# SQRT

### Syntax <a href="#syntax" id="syntax"></a>

#### SQRT(_numeric\_expression_ double) → double <a href="#sqrtnumeric_expression-double--double" id="sqrtnumeric_expression-double--double"></a>

* numeric\_expression: Numeric expression to calculate the square root for.

**Examples**

{% code title="SQRT example" %}
```sql
SELECT SQRT(25.25)
-- 5.024937810560445
```
{% endcode %}

{% code title="SQRT example" %}
```sql
SELECT SQRT(-25.25)
-- NaN
```
{% endcode %}

#### SQRT(_numeric\_expression_ int64) → int64 <a href="#sqrtnumeric_expression-int64--int64" id="sqrtnumeric_expression-int64--int64"></a>

* numeric\_expression: Numeric expression to calculate the square root for.

**Examples**

{% code title="SQRT example" %}
```sql
SELECT SQRT(25)
-- 5
```
{% endcode %}

#### SQRT(_numeric\_expression_ int32) → int32 <a href="#sqrtnumeric_expression-int32--int32" id="sqrtnumeric_expression-int32--int32"></a>

* numeric\_expression: Numeric expression to calculate the square root for.

**Examples**

{% code title="SQRT example" %}
```sql
SELECT SQRT(25)
-- 5
```
{% endcode %}

#### SQRT(_numeric\_expression_ float) → float <a href="#sqrtnumeric_expression-float--float" id="sqrtnumeric_expression-float--float"></a>

* numeric\_expression: Numeric expression to calculate the square root for.

**Examples**

{% code title="SQRT example" %}
```sql
SELECT SQRT(25.25)
-- 5.024937810560445
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

If the input is a non-negative value, `NaN` will be returned.
