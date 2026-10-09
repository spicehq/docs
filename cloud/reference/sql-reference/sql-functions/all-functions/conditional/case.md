---
description: >-
  Evaluates a list of conditions and returns the first resulting true
  expression. If a true expression is not found, will return the ELSE statement,
  if present, or else will return NULL.
---

# CASE

### Syntax <a href="#syntax" id="syntax"></a>

#### CASE expression WHEN condition THEN result \[ …n ] \[ ELSE result ] END → same as input type <a href="#case-expression-when-condition-then-result--n---else-result--end--same-as-input-type" id="case-expression-when-condition-then-result--n---else-result--end--same-as-input-type"></a>

* expression: A valid SQL expression, typically, a column name.
* condition: A boolean expression. If `condition` is true, then return `result`.
* result: A valid SQL expression.

**Examples**

{% code title="CASE example" %}
```sql
SELECT
CASE categories
     WHEN 'Restaurants' THEN 'food'
     WHEN 'Hotels' THEN 'travel'
     ELSE 'no result'
END
FROM table-name

-- food
```
{% endcode %}

#### CASE WHEN condition THEN result \[ …n ] \[ ELSE result ] END → same as input type <a href="#case-when-condition-then-result--n---else-result--end--same-as-input-type" id="case-when-condition-then-result--n---else-result--end--same-as-input-type"></a>

* condition: A boolean expression. If `expression=condition` is true, then return `result`.
* result: A valid SQL expression.

**Examples**

{% code title="CASE example" %}
```sql
SELECT
CASE
     WHEN categories='Restaurants' THEN 'food'
     WHEN categories='Hotels' THEN 'travel'
     ELSE 'no result'
END
FROM table-name

-- food
```
{% endcode %}
