---
description: >-
  Tests whether an expression matches a pattern. The comparison is
  case-insensitive.
---

# ILIKE

### Syntax <a href="#syntax" id="syntax"></a>

#### ILIKE(_expression_ varchar, _pattern_ varchar) → boolean <a href="#ilikeexpression-varchar-pattern-varchar--boolean" id="ilikeexpression-varchar-pattern-varchar--boolean"></a>

* expression: The expression to compare.
* pattern: The pattern that is compared to the expression.

**Examples**

{% code title="ILIKE example" %}
```sql
SELECT ILIKE ('pancake', '%Cake')
-- True
```
{% endcode %}

#### ILIKE(_expression_ varchar, _pattern_ varchar, _escape\_character_ varchar) → boolean <a href="#ilikeexpression-varchar-pattern-varchar-escape_character-varchar--boolean" id="ilikeexpression-varchar-pattern-varchar-escape_character-varchar--boolean"></a>

* expression: The expression to compare.
* pattern: The pattern that is compared to the expression.
* escape\_character: Putting _escape\_character_ before a wildcard in _pattern_ makes ILIKE treat the wildcard as a regular character when it appears in _expression_.

**Examples**

{% code title="ILIKE example" %}
```sql
SELECT ILIKE ('50%_Off', '%50!%%','!')
-- True
```
{% endcode %}
