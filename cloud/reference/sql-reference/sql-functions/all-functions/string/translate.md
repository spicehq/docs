---
description: >-
  Translates the base expression from the source characters/expression to the
  target characters/expression.
---

# TRANSLATE

### Syntax <a href="#syntax" id="syntax"></a>

#### TRANSLATE(_base\_expression_ varchar, _source\_characters_ varchar, _target\_characters_ varchar) → varchar <a href="#translatebase_expression-varchar-source_characters-varchar-target_characters-varchar--varchar" id="translatebase_expression-varchar-source_characters-varchar-target_characters-varchar--varchar"></a>

* base\_expression: The string to translate.
* source\_characters: A string with all the characters in the base expression that need translating. Each character in this string will be replaced with the corresponding character from the target\_characters expression or ommitted from the string if target\_characters expression has less characters than the source\_characters.
* target\_characters: A string containing all the characters to replace the original characters with.

**Examples**

{% code title="TRANSLATE example" %}
```sql
SELECT TRANSLATE('*a*bX*dYZ*','XYZ*','cef');
-- abcdef
```
{% endcode %}
