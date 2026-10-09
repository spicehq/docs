---
description: Shifts the bits of the numeric expression to the left.
---

# LSHIFT

### Syntax <a href="#syntax" id="syntax"></a>

#### LSHIFT(_expression1_ int32, _expression2_ int32) → int32 <a href="#lshiftexpression1-int32-expression2-int32--int32" id="lshiftexpression1-int32-expression2-int32--int32"></a>

* expression1: Integer to shift.
* expression2: The number of bits to shift by.

**Examples**

{% code title="LSHIFT example" %}
```sql
SELECT LSHIFT(1, 120)
-- 16777216
```
{% endcode %}

{% code title="LSHIFT example" %}
```sql
SELECT LSHIFT(16, 1)
-- 32
```
{% endcode %}

#### LSHIFT(_expression1_ int64, _expression2_ int64) → int64 <a href="#lshiftexpression1-int64-expression2-int64--int64" id="lshiftexpression1-int64-expression2-int64--int64"></a>

* expression1: Integer to shift.
* expression2: The number of bits to shift by.

**Examples**

{% code title="LSHIFT example" %}
```sql
SELECT LSHIFT(1, 120)
-- 16777216
```
{% endcode %}

{% code title="LSHIFT example" %}
```sql
SELECT LSHIFT(16, 1)
-- 32
```
{% endcode %}
