---
description: >-
  Left pads a string with spaces or specified characters to reach the number of
  characters specified as a parameter.
---

# LPAD

### Syntax <a href="#syntax" id="syntax"></a>

#### LPAD(_base\_expression_ varchar, _length_ int64) → varchar <a href="#lpadbase_expression-varchar-length-int64--varchar" id="lpadbase_expression-varchar-length-int64--varchar"></a>

* base\_expression: The expression to pad.
* length: The number of characters to return.

**Examples**

{% code title="LPAD example" %}
```sql
SELECT LPAD('parameter', 11)
--    parameter
```
{% endcode %}

{% code title="LPAD example" %}
```sql
SELECT LPAD('engineering', 6)
-- engine
```
{% endcode %}

#### LPAD(_base\_expression_ varchar, _length_ int64, _pad\_expression_ varchar) → varchar <a href="#lpadbase_expression-varchar-length-int64-pad_expression-varchar--varchar" id="lpadbase_expression-varchar-length-int64-pad_expression-varchar--varchar"></a>

* base\_expression: The expression to pad.
* length: The number of characters to return.
* pad\_expression: Characters to pad the base\_expression with.

**Examples**

{% code title="LPAD example" %}
```sql
SELECT LPAD('parameter', 11, '-')
-- --parameter
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

If pad\_expression is not specified, then the base\_expresion will be padded with spaces. If the length of parameter is less than the length of the base\_expression, the base\_expression will be truncated to the length. If the length parameter + the length of the base\_expression is less than the length of the base\_expression + the length of the pad\_expression, only the subset of the characters from the pad\_expression required to fill the length will be appended to the base\_expression.
