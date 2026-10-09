---
description: Returns the sign of the input expression.
---

# SIGN

### Syntax <a href="#syntax" id="syntax"></a>

#### SIGN(_numeric\_expression_ double) → int <a href="#signnumeric_expression-double--int" id="signnumeric_expression-double--int"></a>

* numeric\_expression: Input expression.

**Examples**

{% code title="SIGN example" %}
```sql
SELECT SIGN(10.3)
-- 1
```
{% endcode %}

#### SIGN(_numeric\_expression_ int32) → int32 <a href="#signnumeric_expression-int32--int32" id="signnumeric_expression-int32--int32"></a>

* numeric\_expression: Input expression.

**Examples**

{% code title="SIGN example" %}
```sql
SELECT SIGN(-5)
-- -1
```
{% endcode %}

#### SIGN(_numeric\_expression_ int64) → int64 <a href="#signnumeric_expression-int64--int64" id="signnumeric_expression-int64--int64"></a>

* numeric\_expression: Input expression.

**Examples**

{% code title="SIGN example" %}
```sql
SELECT SIGN(24)
-- 1
```
{% endcode %}

#### SIGN(_numeric\_expression_ float) → int <a href="#signnumeric_expression-float--int" id="signnumeric_expression-float--int"></a>

* numeric\_expression: Input expression.

**Examples**

{% code title="SIGN example" %}
```sql
SELECT SIGN(0.0)
-- 0
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

1 is returned if the input expression is positive. -1 is returned if the input expression is negative. 0 is returned if the input expression is 0.
