---
description: >-
  Right pads a string with spaces or specified characters to reach the number of
  characters specified as a parameter.
---

# RPAD

### Syntax <a href="#syntax" id="syntax"></a>

#### RPAD(_base\_expression_ varchar, _length_ int64, _pad\_expression_ varchar) → varchar <a href="#rpadbase_expression-varchar-length-int64-pad_expression-varchar--varchar" id="rpadbase_expression-varchar-length-int64-pad_expression-varchar--varchar"></a>

* base\_expression: The expression to pad.
* length: The number of characters to return.
* pad\_expression: Characters to pad the base\_expression with.

**Examples**

{% code title="RPAD example" %}
```sql
SELECT RPAD('spice', 9, '!')
-- spice!!!
```
{% endcode %}

{% code title="RPAD example" %}
```sql
SELECT RPAD('base_', 9, 'expression')
-- base_expr
```
{% endcode %}

#### RPAD(_base\_expression_ varchar, _length_ int64) → varchar <a href="#rpadbase_expression-varchar-length-int64--varchar" id="rpadbase_expression-varchar-length-int64--varchar"></a>

* base\_expression: The expression to pad.
* length: The number of characters to return.

**Examples**

{% code title="RPAD example" %}
```sql
SELECT RPAD('spice', 9)
-- spice    
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

If `pad_expression` is not specified, then the `base_expression` will be padded with spaces. If `length` is less than the length of the `base_expression`, the `base_expression` will be truncated to the length. If the `length` + the length of the `base_expression` is less than the length of the `base_expression` + the length of the `pad_expression`, only the subset of the characters from the `pad_expression` required to fill the length will be appended to the `base_expression`.
