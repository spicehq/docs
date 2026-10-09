---
description: Returns the hexadecimal encoding of an expression.
---

# HEX

### Syntax <a href="#syntax" id="syntax"></a>

#### HEX(_expression_ any primitive) → varchar <a href="#hexexpression-any-primitive--varchar" id="hexexpression-any-primitive--varchar"></a>

* expression: The expression to encode.

**Examples**

{% code title="HEX example" %}
```sql
SELECT HEX('Spice')
-- EXPR$0
-- 4472656D696F
```
{% endcode %}

{% code title="HEX example" %}
```sql
SELECT HEX(2023)
-- EXPR$0
-- 7E7
```
{% endcode %}
