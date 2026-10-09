---
description: >-
  Returns the ASCII code for the first character of a string. If the string is
  empty, 0 is returned.
---

# ASCII

### Syntax <a href="#syntax" id="syntax"></a>

#### ASCII(_expression_ varchar) → int32 <a href="#asciiexpression-varchar--int32" id="asciiexpression-varchar--int32"></a>

* expression: The string for which the ASCII code for the first character in the string is returned.

**Examples**

{% code title="ASCII example" %}
```sql
SELECT ASCII ('SPICE')
-- 68
```
{% endcode %}

{% code title="ASCII example" %}
```sql
SELECT ASCII ('D')
-- 68
```
{% endcode %}

{% code title="ASCII example" %}
```sql
SELECT ASCII ('')
-- 0
```
{% endcode %}
