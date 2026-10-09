---
description: Removes leading spaces or characters from a string.
---

# LTRIM

### Syntax <a href="#syntax" id="syntax"></a>

#### LTRIM(_expression_ varchar, _trim\_expression_ varchar) → varchar <a href="#ltrimexpression-varchar-trim_expression-varchar--varchar" id="ltrimexpression-varchar-trim_expression-varchar--varchar"></a>

* expression: The expression to be trimmed.
* trim\_expression: Leading characters to trim. If this parameter is not specified, then spaces will be trimmed from the input expression.

**Examples**

{% code title="LTRIM example" %}
```sql
SELECT LTRIM('pancake', 'pan')
-- cake
```
{% endcode %}

{% code title="LTRIM example" %}
```sql
SELECT LTRIM('pancake', 'abnp')
-- cake
```
{% endcode %}

{% code title="LTRIM example" %}
```sql
SELECT LTRIM('   spice')
-- spice
```
{% endcode %}
