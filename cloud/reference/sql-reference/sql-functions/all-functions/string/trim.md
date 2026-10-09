---
description: Removes leading, trailing, or both spaces or characters from a string.
---

# TRIM

### Syntax <a href="#syntax" id="syntax"></a>

#### TRIM(LEADING or TRAILING or BOTH _trim\_expression_ varchar FROM _expression_ varchar) → varchar <a href="#trimleading-or-trailing-or-both-trim_expression-varchar-from-expression-varchar--varchar" id="trimleading-or-trailing-or-both-trim_expression-varchar-from-expression-varchar--varchar"></a>

* trim\_expression: The characters to trim.
* expression: The expression to be trimmed.

**Examples**

{% code title="TRIM example" %}
```sql
SELECT TRIM('    pancake    ')
-- pancake
```
{% endcode %}

{% code title="TRIM example" %}
```sql
SELECT TRIM(leading 'pan' from 'pancake')
-- cake
```
{% endcode %}

{% code title="TRIM example" %}
```sql
SELECT TRIM(trailing 'cake' from 'pancake')
-- pan
```
{% endcode %}

{% code title="TRIM example" %}
```sql
SELECT TRIM(both 'pan' from 'pancake pan')
-- cake
```
{% endcode %}

{% code title="TRIM example" %}
```sql
SELECT TRIM('pan' from 'pancake pan')
-- cake
```
{% endcode %}

{% code title="TRIM example" %}
```sql
SELECT TRIM('abnp' from 'pancake pan')
-- cake
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

If you do not specify a keyword before `trim_expression`, it defaults to BOTH. If you do not specify `trim_expression`, it defaults to a space. Organizations using Oracle will always receive a `NULL` return if this function is used with an empty string.
