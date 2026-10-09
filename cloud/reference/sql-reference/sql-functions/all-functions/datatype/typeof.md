---
description: Reports the type (in string format) of the input expression.
---

# TYPEOF

### Syntax <a href="#syntax" id="syntax"></a>

#### TYPEOF(_input_ any) → varchar <a href="#typeofinput-any--varchar" id="typeofinput-any--varchar"></a>

* input: An expression for which the type is returned.

**Examples**

{% code title="TYPEOF example" %}
```sql
SELECT TYPEOF(TRUE)
-- BIT
```
{% endcode %}

{% code title="TYPEOF example" %}
```sql
SELECT TYPEOF(100)
-- INT
```
{% endcode %}

{% code title="TYPEOF example" %}
```sql
SELECT TYPEOF(98.76)
-- DECIMAL
```
{% endcode %}

{% code title="TYPEOF example" %}
```sql
SELECT TYPEOF('2021-09-14')
-- VARCHAR
```
{% endcode %}
