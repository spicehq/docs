---
description: >-
  Returns a substring of an expression before the specified number of delimiters
  occurs.
---

# SUBSTRING\_INDEX

### Syntax <a href="#syntax" id="syntax"></a>

#### SUBSTRING\_INDEX(_expression_ varchar, _delimiter_ varchar, _count_ integer) → varchar <a href="#substring_indexexpression-varchar-delimiter-varchar-count-integer--varchar" id="substring_indexexpression-varchar-delimiter-varchar-count-integer--varchar"></a>

* expression: Base expression to extract substring from.
* delimiter: The string to search for.
* count: An `INTEGER` expression to count the delimiters.

**Examples**

{% code title="SUBSTRING_INDEX example" %}
```sql
SELECT SUBSTRING_INDEX('spice.ai', '.', 2)
-- EXPR$0
-- spice
```
{% endcode %}

{% code title="SUBSTRING_INDEX example" %}
```sql
SELECT SUBSTRING_INDEX('spice.ai', '.', -2)
-- EXPR$0
-- spice.ai
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

This function performs a case-sensitive match when searching for the delimiter. The `count` expression can be a positive or negative number. If positive, this function returns the characters from the left of the expression up to the `count` of occurrences of the delimiter. If negative, this function returns the characters from the right of the expression up to the `count` of occurrences of the delimiter.
