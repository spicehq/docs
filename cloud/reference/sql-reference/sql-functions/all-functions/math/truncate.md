---
description: >-
  Rounds the input expression down the nearest of equal integer depending on the
  specified number of places before or after the decimal point.
---

# TRUNCATE

### Syntax <a href="#syntax" id="syntax"></a>

#### TRUNCATE(_numeric\_expression_ float) → int <a href="#truncatenumeric_expression-float--int" id="truncatenumeric_expression-float--int"></a>

* numeric\_expression: The numeric expression to truncate.

**Examples**

{% code title="TRUNCATE example" %}
```sql
SELECT TRUNCATE(987.65)
-- 987
```
{% endcode %}

#### TRUNCATE(_numeric\_expression_ double) → int <a href="#truncatenumeric_expression-double--int" id="truncatenumeric_expression-double--int"></a>

* numeric\_expression: The numeric expression to truncate.

**Examples**

{% code title="TRUNCATE example" %}
```sql
SELECT TRUNCATE(987.87)
-- 987
```
{% endcode %}

#### TRUNCATE(_numeric\_expression_ int32) → int32 <a href="#truncatenumeric_expression-int32--int32" id="truncatenumeric_expression-int32--int32"></a>

* numeric\_expression: The numeric expression to truncate.

**Examples**

{% code title="TRUNCATE example" %}
```sql
SELECT TRUNCATE(2021)
-- 2021
```
{% endcode %}

#### TRUNCATE(_numeric\_expression_ int64) → int64 <a href="#truncatenumeric_expression-int64--int64" id="truncatenumeric_expression-int64--int64"></a>

* numeric\_expression: The numeric expression to truncate.

**Examples**

{% code title="TRUNCATE example" %}
```sql
SELECT TRUNCATE(2021)
-- 2021
```
{% endcode %}

#### TRUNCATE(_numeric\_expression_ decimal(0,0), _scale\_expression_ int32) → decimal(0,0) <a href="#truncatenumeric_expression-decimal00-scale_expression-int32--decimal00" id="truncatenumeric_expression-decimal00-scale_expression-int32--decimal00"></a>

* numeric\_expression: The numeric expression to truncate.
* scale\_expression: sample parameter description

**Examples**

{% code title="TRUNCATE example" %}
```sql
SELECT TRUNCATE(89.2283211, 2)
-- 89.22
```
{% endcode %}

#### TRUNCATE(_numeric\_expression_ int64, _scale\_expression_ int32) → int64 <a href="#truncatenumeric_expression-int64-scale_expression-int32--int64" id="truncatenumeric_expression-int64-scale_expression-int32--int64"></a>

* numeric\_expression: The numeric expression to truncate.
* scale\_expression: sample parameter description

**Examples**

{% code title="TRUNCATE example" %}
```sql
SELECT TRUNCATE(2021, -1)
-- 2020
```
{% endcode %}

#### TRUNCATE(_numeric\_expression_ decimal(0,0)) → decimal(0,0) <a href="#truncatenumeric_expression-decimal00--decimal00" id="truncatenumeric_expression-decimal00--decimal00"></a>

* numeric\_expression: The numeric expression to truncate.

**Examples**

{% code title="TRUNCATE example" %}
```sql
SELECT TRUNCATE(9.7)
-- 9
```
{% endcode %}

#### TRUNCATE(_numeric\_expression_ float, _scale\_expression_ int32) → float <a href="#truncatenumeric_expression-float-scale_expression-int32--float" id="truncatenumeric_expression-float-scale_expression-int32--float"></a>

* numeric\_expression: The numeric expression to truncate.
* scale\_expression: The decimal place to round to.

**Examples**

{% code title="TRUNCATE example" %}
```sql
SELECT TRUNCATE(78.9823, 2)
-- 78.98
```
{% endcode %}

#### TRUNCATE(_numeric\_expression_ int32, _scale\_expression_ int32) → int32 <a href="#truncatenumeric_expression-int32-scale_expression-int32--int32" id="truncatenumeric_expression-int32-scale_expression-int32--int32"></a>

* numeric\_expression: The numeric expression to truncate.
* scale\_expression: The decimal place to round to.

**Examples**

{% code title="TRUNCATE example" %}
```sql
SELECT TRUNCATE(4356, -2)
-- 4300
```
{% endcode %}

#### TRUNCATE(_numeric\_expression_ double, _scale\_expression_ int32) → double <a href="#truncatenumeric_expression-double-scale_expression-int32--double" id="truncatenumeric_expression-double-scale_expression-int32--double"></a>

* numeric\_expression: The numeric expression to truncate.
* scale\_expression: The decimal place to round to.

**Examples**

{% code title="TRUNCATE example" %}
```sql
SELECT TRUNCATE(987.65, 1)
-- 987.6
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

To round to a place after the decimal point, specify the position as a positive number. To round to a place before the decimal point, specify the position as a negative number. To round to the nearest whole number, specify 0. If no decimal position is specified, then the function rounds to the nearest whole number (assumes 0 as the scale expression).
