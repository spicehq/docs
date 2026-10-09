---
description: Returns the character length of the input string.
---

# CHAR\_LENGTH

### Syntax <a href="#syntax" id="syntax"></a>

#### CHAR\_LENGTH(_expression_ STRING) → INTEGER <a href="#char_lengthexpression-string--integer" id="char_lengthexpression-string--integer"></a>

* expression: The expression (VARCHAR) to determine character length for.

**Examples**

{% code title="CHAR_LENGTH example" %}
```sql
SELECT CHAR_LENGTH('get the char length')
-- 19
```
{% endcode %}

{% code title="CHAR_LENGTH example" %}
```sql
SELECT CHAR_LENGTH('SPICE')
-- 5
```
{% endcode %}
