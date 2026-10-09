---
description: >-
  Converts a value of one data type to another data type. This function behaves
  similarly to the TO_<data_type> (i.e. TO_TIMESTAMP) functions.
---

# CAST

### Syntax <a href="#syntax" id="syntax"></a>

#### CAST(_expression_ Any type, _data\_type_ Any type) → Type specified as data\_type parameter <a href="#castexpression-any-type-data_type-any-type--type-specified-as-data_type-parameter" id="castexpression-any-type-data_type-any-type--type-specified-as-data_type-parameter"></a>

* expression: The expression that you want to convert.
* data\_type: The name of the data type that you want to convert the input expression to.

**Examples**

{% code title="CAST example" %}
```sql
SELECT CAST(3.14150 AS INTEGER)
-- 3
```
{% endcode %}

{% code title="CAST example" %}
```sql
SELECT CAST(.167 AS INTEGER)
-- 0
```
{% endcode %}

{% code title="CAST example" %}
```sql
SELECT CAST('2021-04-03' AS DATE)
-- 2021-04-03
```
{% endcode %}

\
