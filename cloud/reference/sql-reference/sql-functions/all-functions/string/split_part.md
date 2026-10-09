---
description: Splits a given string at a specified character and returns the requested part.
---

# SPLIT\_PART

### Syntax <a href="#syntax" id="syntax"></a>

#### SPLIT\_PART(_expression_ varchar, _delimiter_ varchar, _part\_number_ int32) → varchar <a href="#split_partexpression-varchar-delimiter-varchar-part_number-int32--varchar" id="split_partexpression-varchar-delimiter-varchar-part_number-int32--varchar"></a>

* expression: Input expression.
* delimiter: String representing the delimiter to split the input expression by.
* part\_number: Requested part of the split. Must be an integer greater than zero.

**Examples**

{% code title="SPLIT_PART example" %}
```sql
SELECT SPLIT_PART('127.0.0.1', '.', 1)
-- 127
```
{% endcode %}
