---
description: Repeats the given string n times.
---

# REPEATSTR

### Syntax <a href="#syntax" id="syntax"></a>

#### REPEATSTR(_expression_ varchar, _nTimes_ int32) → varchar <a href="#repeatstrexpression-varchar-ntimes-int32--varchar" id="repeatstrexpression-varchar-ntimes-int32--varchar"></a>

* expression: String/characters to repeat.
* nTimes: Number of times the string should be repeated.

**Examples**

{% code title="REPEATSTR example" %}
```sql
SELECT REPEATSTR('a ', 5)
-- a a a a a
```
{% endcode %}
