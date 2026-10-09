---
description: Removes trailing spaces or characters from a string.
---

# RTRIM

### Syntax <a href="#syntax" id="syntax"></a>

#### RTRIM(_expression_ varchar, _trim\_expression_ varchar) → varchar <a href="#rtrimexpression-varchar-trim_expression-varchar--varchar" id="rtrimexpression-varchar-trim_expression-varchar--varchar"></a>

* expression: The expression to be trimmed.
* trim\_expression: Trailing characters to trim. If this parameter is not specified, then spaces will be trimmed from the input expression.

**Examples**

{% code title="RTRIM example" %}
```sql
SELECT RTRIM('pancake', 'cake')
-- pan
```
{% endcode %}

{% code title="RTRIM example" %}
```sql
SELECT RTRIM('pancake pan', 'abnp')
-- pancake
```
{% endcode %}

{% code title="RTRIM example" %}
```sql
SELECT RTRIM('spice   ')
-- spice
```
{% endcode %}
