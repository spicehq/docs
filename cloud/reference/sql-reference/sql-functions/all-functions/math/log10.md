---
description: Returns the log base 10 of the numeric input expression.
---

# LOG10

### Syntax <a href="#syntax" id="syntax"></a>

#### LOG10(_expression_ DOUBLE) → DOUBLE <a href="#log10expression-double--double" id="log10expression-double--double"></a>

* expression: The value for which you want to calculate the log.

**Examples**

{% code title="LOG10 example" %}
```sql
SELECT LOG10(20.5)
-- 1.3117538610557542
```
{% endcode %}

#### LOG10(_expression_ int64) → DOUBLE <a href="#log10expression-int64--double" id="log10expression-int64--double"></a>

* expression: The value for which you want to calculate the log.

**Examples**

{% code title="LOG10 example" %}
```sql
SELECT LOG10(100)
-- 2.0
```
{% endcode %}

#### LOG10(_expression_ int32) → DOUBLE <a href="#log10expression-int32--double" id="log10expression-int32--double"></a>

* expression: The value for which you want to calculate the log.

**Examples**

{% code title="LOG10 example" %}
```sql
SELECT LOG10(100)
-- 2.0
```
{% endcode %}

#### LOG10(_expression_ float) → DOUBLE <a href="#log10expression-float--double" id="log10expression-float--double"></a>

* expression: The value for which you want to calculate the log.

**Examples**

{% code title="LOG10 example" %}
```sql
SELECT LOG10(20.5)
-- 1.3117538610557542
```
{% endcode %}
