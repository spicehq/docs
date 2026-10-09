---
description: >-
  Returns the position of the first occurrence of a substring within another
  string
---

# POSITION

### Syntax <a href="#syntax" id="syntax"></a>

#### POSITION(_substr_ string IN _expression_ string) → integer <a href="#positionsubstr-string-in-expression-string--integer" id="positionsubstr-string-in-expression-string--integer"></a>

* substr: The substring to search for in the expression.
* expression: The input expression to search.

**Examples**

{% code title="POSITION example" %}
```sql
SELECT POSITION('an' in 'banana')
-- 2
```
{% endcode %}

{% code title="POSITION example" %}
```sql
SELECT POSITION('no' in 'banana')
-- 0
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

Searches for the first occurrence of the first argument in the second argument and if found, returns the position the of the first argument in the second argument. The first character in a string is position 1. Returns 0 if the substring is not found in the expression.
