---
description: >-
  Removes all occurrences of a specified substring and replaces them with
  another string.
---

# REPLACE

### Syntax <a href="#syntax" id="syntax"></a>

#### REPLACE(_string\_expression_ varchar, _pattern_ varchar, _replacement_ varchar) → varchar <a href="#replacestring_expression-varchar-pattern-varchar-replacement-varchar--varchar" id="replacestring_expression-varchar-pattern-varchar-replacement-varchar--varchar"></a>

* string\_expression: String expression in which to do the replacements.
* pattern: The substring you want replaced in the string\_expression.
* replacement: The string to replace the occurrences of the pattern substring with.

**Examples**

{% code title="REPLACE example" %}
```sql
SELECT REPLACE('THE CATATONIC CAT', 'CAT', 'DOG')
-- sample return 1
```
{% endcode %}
