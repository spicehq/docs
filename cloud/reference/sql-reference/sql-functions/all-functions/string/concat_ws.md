---
description: >-
  Concatenate with separator. Returns a string resulting from the joining of two
  or more string values in an end-to-end manner. Uses the first argument as the
  separator between each string.
---

# CONCAT\_WS

### Syntax <a href="#syntax" id="syntax"></a>

#### CONCAT\_WS(separator, expression1, expression2, \[ … expressionN ]) → string <a href="#concat_wsseparator-expression1-expression2---expressionn---string" id="concat_wsseparator-expression1-expression2---expressionn---string"></a>

* separator: An expression of any character type, such as `binary` or `varchar`. However, all arguments must be the same data type.
* expression: An expression can be any data type. All arguments must be the same data type.

**Examples**

{% code title="CONCAT_WS example" %}
```sql
SELECT CONCAT_WS('-', 'cat', 'dog', 'bird')

-- cat-dog-bird
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

This function does not append a separator if there is only one argument. If the separator is `NULL`, then the result is `NULL`. If any of the expressions are `NULL`, they are ignored. If only the separator is provided, or all provided expressions are `NULL`, then an empty string is returned.
