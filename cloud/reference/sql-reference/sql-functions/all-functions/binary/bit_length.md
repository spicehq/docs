---
description: Gets length of bits of the input expression.
---

# BIT\_LENGTH

### Syntax <a href="#syntax" id="syntax"></a>

#### BIT\_LENGTH(_expression_ BINARY, VARCHAR) → BINARY <a href="#bit_lengthexpression-binary-varchar--binary" id="bit_lengthexpression-binary-varchar--binary"></a>

* expression: A binary or varchar expression

**Examples**

{% code title="BIT_LENGTH example" %}
```sql
SELECT BIT_LENGTH(1010)
-- 32
```
{% endcode %}

{% code title="BIT_LENGTH example" %}
```sql
SELECT BIT_LENGTH('SPICE')
-- 48
```
{% endcode %}

{% code title="BIT_LENGTH example" %}
```sql
SELECT BIT_LENGTH('abc')
-- 24
```
{% endcode %}

{% code title="BIT_LENGTH example" %}
```sql
SELECT BIT_LENGTH(NULL)
-- None
```
{% endcode %}
