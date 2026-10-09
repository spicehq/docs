---
description: Builds a string by repeating the input for the specified number of times
---

# REPEAT

### Syntax <a href="#syntax" id="syntax"></a>

#### REPEAT(_expression_ varchar, _nTimes_ int32) → varchar <a href="#repeatexpression-varchar-ntimes-int32--varchar" id="repeatexpression-varchar-ntimes-int32--varchar"></a>

* expression: The input string from which the output string is built.
* nTimes: The number of times the input expression should be repeated.

**Examples**

{% code title="REPEAT example" %}
```sql
SELECT REPEAT('abc', 3)
-- abcabcabc
```
{% endcode %}
