---
description: Trims leading and trailing characters from a string.
---

# BTRIM

### Syntax <a href="#syntax" id="syntax"></a>

#### BTRIM(_expression_ string, _trim\_text_ string) → string <a href="#btrimexpression-string-trim_text-string--string" id="btrimexpression-string-trim_text-string--string"></a>

* expression: String expression to be trimmed.
* trim\_text: Leading and trailing characters to trim from the input expression. If this parameter is not specified, then spaces will be trimmed from the input expression.

**Examples**

{% code title="BTRIM example" %}
```sql
SELECT BTRIM('spice ')
-- spice
```
{% endcode %}

{% code title="BTRIM example" %}
```sql
SELECT BTRIM('~/~/~/spice~', '~')
-- /~/~/spice
```
{% endcode %}

{% code title="BTRIM example" %}
```sql
SELECT BTRIM('---spice-', '-')
-- spice
```
{% endcode %}

{% code title="BTRIM example" %}
```sql
SELECT BTRIM('stringvalue','string')
-- value
```
{% endcode %}

{% code title="BTRIM example" %}
```sql
SELECT BTRIM('pancake pan','abnp')
-- cake
```
{% endcode %}
