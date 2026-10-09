---
description: >-
  Returns the position of the first occurrence of a string when it is contained
  in another string. If no such occurrence is found, a zero is returned. The
  comparison is case-sensitive.
---

# INSTR

### Syntax <a href="#syntax" id="syntax"></a>

#### INSTR(_expression1_ string, _expression2_ string) → int <a href="#instrexpression1-string-expression2-string--int" id="instrexpression1-string-expression2-string--int"></a>

* expression1: The input expression to search.
* expression2: The string to search for in the specified expression.

**Examples**

{% code title="INSTR example" %}
```sql
SELECT INSTR('SPICE', 'S')
-- EXPR$0
-- 1
```
{% endcode %}

{% code title="INSTR example" %}
```sql
SELECT INSTR('SPICE', 'd')
-- EXPR$0
-- 0
```
{% endcode %}
