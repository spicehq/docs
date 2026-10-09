---
description: >-
  Compares two expressions to determine whether they have the same or different
  values. NULLs are considered as comparable values.
---

# IS \[NOT] DISTINCT FROM

### Syntax <a href="#syntax" id="syntax"></a>

#### IS \[NOT] DISTINCT FROM(_expression_ any) → boolean <a href="#is-not-distinct-fromexpression-any--boolean" id="is-not-distinct-fromexpression-any--boolean"></a>

* expression: Can be a general expression of any Spice-supported data type.

**Examples**

{% code title="IS [NOT] DISTINCT FROM example" %}
```sql
SELECT NULL IS DISTINCT
FROM NULL

-- False
```
{% endcode %}

{% code title="IS [NOT] DISTINCT FROM example" %}
```sql
SELECT NULL IS NOT DISTINCT
FROM NULL

-- True
```
{% endcode %}
