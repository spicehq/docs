---
description: >-
  Searches for the first occurrence of the substring in the given expression and
  returns the position of where the substring begins. Searching binary values is
  also supported.
---

# STRPOS

### Syntax <a href="#syntax" id="syntax"></a>

#### STRPOS(_expression_ varchar, _substring_ varchar) → int32 <a href="#strposexpression-varchar-substring-varchar--int32" id="strposexpression-varchar-substring-varchar--int32"></a>

* expression: The expression to search.
* substring: The substring to search the expression for.

**Examples**

{% code title="STRPOS example" %}
```sql
SELECT STRPOS('spice cloud service', 'cloud')
-- 8
```
{% endcode %}

{% code title="STRPOS example" %}
```sql
SELECT STRPOS(1001111, 00)
-- 2
```
{% endcode %}

{% code title="STRPOS example" %}
```sql
SELECT STRPOS('spice cloud service', 'sql')
-- 0
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

If the substring is not found, the function returns 0. The data type of both parameters must match; specifically, they should both be either strings or binary values.
