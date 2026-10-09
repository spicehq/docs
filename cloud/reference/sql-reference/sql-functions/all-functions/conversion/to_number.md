---
description: Converts a string into a number (double) in the specified format.
---

# TO\_NUMBER

### Syntax <a href="#syntax" id="syntax"></a>

#### TO\_NUMBER(_expression_ varchar, _format_ varchar) → double <a href="#to_numberexpression-varchar-format-varchar--double" id="to_numberexpression-varchar-format-varchar--double"></a>

* expression: String to convert to a number.
* format: Format for number conversion.

**Examples**

{% code title="TO_NUMBER example" %}
```sql
SELECT TO_NUMBER('12374.0023', '#####.###')
-- 12374.002
```
{% endcode %}

{% code title="TO_NUMBER example" %}
```sql
SELECT TO_NUMBER('12374', '#####')
-- 12374.0
```
{% endcode %}
