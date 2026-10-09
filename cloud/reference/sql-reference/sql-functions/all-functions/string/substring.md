---
description: >-
  Returns the portion of the string from the specified base expression starting
  at the specified characters.
---

# SUBSTRING

### Syntax <a href="#syntax" id="syntax"></a>

#### SUBSTRING(_string\_expression_ varchar, _offset_ int64) → varchar <a href="#substringstring_expression-varchar-offset-int64--varchar" id="substringstring_expression-varchar-offset-int64--varchar"></a>

* string\_expression: Base expression to extract substring from.
* offset: The offset from which the substring starts.

**Examples**

{% code title="SUBSTRING example" %}
```sql
SELECT SUBSTRING('spice user 1 2 3', 12)
-- 1 2 3
```
{% endcode %}

#### SUBSTRING(_string\_expression_ varchar, _offset_ int64, _length_ int64) → varchar <a href="#substringstring_expression-varchar-offset-int64-length-int64--varchar" id="substringstring_expression-varchar-offset-int64-length-int64--varchar"></a>

* string\_expression: Base expression to extract substring from.
* offset: The offset from which the substring starts.
* length: The length limit of the substring.

**Examples**

{% code title="SUBSTRING example" %}
```sql
SELECT SUBSTRING('base expression', 6, 4)
-- expr
```
{% endcode %}

#### SUBSTRING(_string\_expression_ varchar, _pattern_ varchar) → varchar <a href="#substringstring_expression-varchar-pattern-varchar--varchar" id="substringstring_expression-varchar-pattern-varchar--varchar"></a>

* string\_expression: Base expression to extract substring from.
* pattern: The offset from which the substring starts.

**Examples**

{% code title="SUBSTRING example" %}
```sql
SELECT SUBSTRING('spice user 123', '[0-9]+')
-- 123
```
{% endcode %}
