---
description: >-
  Returns the left-most substring. The function name must be enclosed in double
  quotes ("LEFT").
---

# LEFT

### Syntax <a href="#syntax" id="syntax"></a>

#### LEFT(_expression_ varchar, _length_ int64) → varchar <a href="#leftexpression-varchar-length-int64--varchar" id="leftexpression-varchar-length-int64--varchar"></a>

* expression: String input parameter
* length: Number of characters on the left to return.

**Examples**

{% code title="LEFT example" %}
```sql
SELECT "LEFT"('Spice - SQL Engine', -13)
-- Spice
```
{% endcode %}

{% code title="LEFT example" %}
```sql
SELECT "LEFT"('Spice - SQL Engine', 5)
-- Spice
```
{% endcode %}
