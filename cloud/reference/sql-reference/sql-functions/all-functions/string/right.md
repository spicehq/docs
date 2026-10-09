---
description: >-
  Returns the right-most substring. The function name must be enclosed in double
  quotes (“RIGHT”).
---

# RIGHT

### Syntax <a href="#syntax" id="syntax"></a>

#### RIGHT(_string_ varchar, _length_ int64) → varchar <a href="#rightstring-varchar-length-int64--varchar" id="rightstring-varchar-length-int64--varchar"></a>

* string: String input parameter.
* length: Number of characters on the right to return.

**Examples**

{% code title="RIGHT example" %}
```sql
SELECT "RIGHT"('Spice - SQL Engine', 6)
-- Engine
```
{% endcode %}
