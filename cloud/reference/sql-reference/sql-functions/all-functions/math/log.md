---
description: >-
  Returns the logarithm of the numeric input expression. If no base is
  specified, the natural log (ln) will be calculated.
---

# LOG

### Syntax <a href="#syntax" id="syntax"></a>

#### LOG(_base\_expression_ FLOAT, _expression_ FLOAT) → DOUBLE <a href="#logbase_expression-float-expression-float--double" id="logbase_expression-float-expression-float--double"></a>

* base\_expression: The base to use.
* expression: The value to calculate the log.

**Examples**

{% code title="LOG example" %}
```sql
SELECT LOG(20.5, 1.5)
-- 0.1342410830900514
```
{% endcode %}

#### LOG(_base\_expression_ DOUBLE, _expression_ DOUBLE) → DOUBLE <a href="#logbase_expression-double-expression-double--double" id="logbase_expression-double-expression-double--double"></a>

* base\_expression: The base to use.
* expression: The value for which you want to calculate the log.

**Examples**

{% code title="LOG example" %}
```sql
SELECT LOG(20.5, 1.5)
-- 0.1342410830900514
```
{% endcode %}

#### LOG(_expression_ int64) → DOUBLE <a href="#logexpression-int64--double" id="logexpression-int64--double"></a>

* expression: The value for which you want to calculate the log.

**Examples**

{% code title="LOG example" %}
```sql
SELECT LOG(10)
-- 2.302585092994046
```
{% endcode %}

#### LOG(_base\_expression_ int64, _expression_ int64) → DOUBLE <a href="#logbase_expression-int64-expression-int64--double" id="logbase_expression-int64-expression-int64--double"></a>

* base\_expression: The base to use.
* expression: The value for which you want to calculate the log.

**Examples**

{% code title="LOG example" %}
```sql
SELECT LOG(10, 2)
-- 0.30102999566398114
```
{% endcode %}

#### LOG(_expression_ int32) → DOUBLE <a href="#logexpression-int32--double" id="logexpression-int32--double"></a>

* expression: The value for which you want to calculate the log.

**Examples**

{% code title="LOG example" %}
```sql
SELECT LOG(10)
-- 2.302585092994046
```
{% endcode %}

#### LOG(_base\_expression_ int32, _expression_ int32) → DOUBLE <a href="#logbase_expression-int32-expression-int32--double" id="logbase_expression-int32-expression-int32--double"></a>

* base\_expression: The base to use.
* expression: The value for which you want to calculate the log.

**Examples**

{% code title="LOG example" %}
```sql
SELECT LOG(10, 2)
-- 0.30102999566398114
```
{% endcode %}

#### LOG(_expression_ float) → DOUBLE <a href="#logexpression-float--double" id="logexpression-float--double"></a>

* expression: The value for which you want to calculate the log.

**Examples**

{% code title="LOG example" %}
```sql
SELECT LOG(12.5)
-- 2.5257286443082556
```
{% endcode %}

#### LOG(_expression_ double) → DOUBLE <a href="#logexpression-double--double" id="logexpression-double--double"></a>

* expression: The value for which you want to calculate the log.

**Examples**

{% code title="LOG example" %}
```sql
SELECT LOG(12.5)
-- 2.5257286443082556
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

If base\_expression is 1. If base\_expression expression is less than 0, NaN will be returned. If the expression input is 0, `-Infinity` is returned. If the expression input is less than 0, NaN will be returned.
