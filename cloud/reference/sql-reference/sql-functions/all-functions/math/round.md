---
description: >-
  Returns the rounded value for the inputted value. If no scale is specified,
  the closest whole number is returned.
---

# ROUND

### Syntax <a href="#syntax" id="syntax"></a>

#### ROUND(_numeric\_expression_ decimal(0,0), _scale_ int32) → decimal(0,0) <a href="#roundnumeric_expression-decimal00-scale-int32--decimal00" id="roundnumeric_expression-decimal00-scale-int32--decimal00"></a>

* numeric\_expression: Numeric value to round.
* scale: The decimal place to round.

**Examples**

{% code title="ROUND example" %}
```sql
SELECT ROUND(-24.35, -1)
-- -24.4
```
{% endcode %}

{% code title="ROUND example" %}
```sql
SELECT ROUND(24.35, 1)
-- 24.4
```
{% endcode %}

#### ROUND(_numeric\_expression_ int32, _scale_ int32) → int32 <a href="#roundnumeric_expression-int32-scale-int32--int32" id="roundnumeric_expression-int32-scale-int32--int32"></a>

* numeric\_expression: Numeric value to round.
* scale: The decimal place to round.

**Examples**

{% code title="ROUND example" %}
```sql
SELECT ROUND(24, 0)
-- 0
```
{% endcode %}

{% code title="ROUND example" %}
```sql
SELECT ROUND(-24, -1)
-- -20
```
{% endcode %}

#### ROUND(_numeric\_expression_ int32) → int32 <a href="#roundnumeric_expression-int32--int32" id="roundnumeric_expression-int32--int32"></a>

* numeric\_expression: Numeric value to round.

**Examples**

{% code title="ROUND example" %}
```sql
SELECT ROUND(24)
-- 24
```
{% endcode %}

#### ROUND(_numeric\_expression_ double) → double <a href="#roundnumeric_expression-double--double" id="roundnumeric_expression-double--double"></a>

* numeric\_expression: Numeric value to round.

**Examples**

{% code title="ROUND example" %}
```sql
SELECT ROUND(24.35)
-- 24
```
{% endcode %}

#### ROUND(_inputHolder_ decimal(0,0)) → decimal(0,0) <a href="#roundinputholder-decimal00--decimal00" id="roundinputholder-decimal00--decimal00"></a>

* inputHolder: sample parameter description

**Examples**

{% code title="ROUND example" %}
```sql
sample call 1
-- sample return 1
```
{% endcode %}

#### ROUND(_in_ float) → float <a href="#roundin-float--float" id="roundin-float--float"></a>

* in: sample parameter description

**Examples**

{% code title="ROUND example" %}
```sql
sample call 1
-- sample return 1
```
{% endcode %}

#### ROUND(_in_ int64) → int64 <a href="#roundin-int64--int64" id="roundin-int64--int64"></a>

* in: sample parameter description

**Examples**

{% code title="ROUND example" %}
```sql
sample call 1
-- sample return 1
```
{% endcode %}

#### ROUND(_input1_ int64, _input2_ int32) → int64 <a href="#roundinput1-int64-input2-int32--int64" id="roundinput1-int64-input2-int32--int64"></a>

* input1: sample parameter description
* input2: sample parameter description

**Examples**

{% code title="ROUND example" %}
```sql
sample call 1
-- sample return 1
```
{% endcode %}

#### ROUND(_input1_ double, _input2_ int32) → double <a href="#roundinput1-double-input2-int32--double" id="roundinput1-double-input2-int32--double"></a>

* input1: sample parameter description
* input2: sample parameter description

**Examples**

{% code title="ROUND example" %}
```sql
sample call 1
-- sample return 1
```
{% endcode %}

#### ROUND(_numeric\_expression_ float, _input2_ int32) → float <a href="#roundnumeric_expression-float-input2-int32--float" id="roundnumeric_expression-float-input2-int32--float"></a>

* numeric\_expression: sample parameter description
* input2: sample parameter description

**Examples**

{% code title="ROUND example" %}
```sql
sample call 1
-- sample return 1
```
{% endcode %}
