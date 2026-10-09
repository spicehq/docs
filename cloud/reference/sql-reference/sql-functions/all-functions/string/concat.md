---
description: Concatenates two or more strings. NULL values are ignored.
---

# CONCAT

### Syntax <a href="#syntax" id="syntax"></a>

#### CONCAT(_expression1_ string, _expression2_ string, _expressionN_ string) → string <a href="#concatexpression1-string-expression2-string-expressionn-string--string" id="concatexpression1-string-expression2-string-expressionn-string--string"></a>

* expression1: First string expression.
* expression2: Second string expression.
* expressionN: Nth string expression.

**Examples**

{% code title="CONCAT example" %}
```sql
SELECT CONCAT('CON', 'CAT')
-- CONCAT
```
{% endcode %}

{% code title="CONCAT example" %}
```sql
SELECT CONCAT('con', 'cat', NULL)
-- concat
```
{% endcode %}
