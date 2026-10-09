---
description: >-
  Returns the length of an input string. If the character encoding isn’t
  specified, it assumes to UTF8.
---

# LENGTH

### Syntax <a href="#syntax" id="syntax"></a>

#### LENGTH(_expression_ varchar) → int32 <a href="#lengthexpression-varchar--int32" id="lengthexpression-varchar--int32"></a>

* expression: String expression to determine the length of.

**Examples**

{% code title="LENGTH example" %}
```sql
SELECT LENGTH('SPICE')
-- 5
```
{% endcode %}
