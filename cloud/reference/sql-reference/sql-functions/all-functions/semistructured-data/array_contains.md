---
description: Returns whether a list contains a given value.
---

# ARRAY\_CONTAINS

### Syntax <a href="#syntax" id="syntax"></a>

#### ARRAY\_CONTAINS(_list_ LIST, _value_ any) → boolean <a href="#array_containslist-list-value-any--boolean" id="array_containslist-list-value-any--boolean"></a>

* list: The list to search.
* value: An expression of a type that is comparable with the LIST.

**Examples**

{% code title="ARRAY_CONTAINS example" %}
```sql
SELECT ARRAY_CONTAINS(CONVERT_FROM('["apple", "pear", "banana"]', 'json'), NULL)
-- null
```
{% endcode %}

{% code title="ARRAY_CONTAINS example" %}
```sql
SELECT ARRAY_CONTAINS(CONVERT_FROM('["apple", "pear", "banana"]', 'json'), 'pear')
-- true
```
{% endcode %}

{% code title="ARRAY_CONTAINS example" %}
```sql
SELECT ARRAY_CONTAINS(CONVERT_FROM('["apple", "pear", "banana"]', 'json'), 'grape')
-- false
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

If _value_ is `NULL`, the result is `NULL`. If `NULL` is in _list_ and _value_ is not in _list_, the result is `NULL`. If _value_ is present in the _list_, the result is `TRUE`. Otherwise, the result is `FALSE`.

\
