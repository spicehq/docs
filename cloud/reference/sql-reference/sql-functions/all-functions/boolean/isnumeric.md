---
description: >-
  Determines whether an expression is a valid numeric type (DECIMAL, DOUBLE,
  INT, BIGINT, VARBINARY).
---

# ISNUMERIC

### Syntax <a href="#syntax" id="syntax"></a>

#### ISNUMERIC(_expression_ any) → boolean <a href="#isnumericexpression-any--boolean" id="isnumericexpression-any--boolean"></a>

* expression: Can be a general expression of any Spice-supported data type.

**Examples**

{% code title="ISNUMERIC example" %}
```sql
SELECT ISNUMERIC('13579')
-- True
```
{% endcode %}

{% code title="ISNUMERIC example" %}
```sql
SELECT ISNUMERIC('Hello World!')
-- False
```
{% endcode %}

{% code title="ISNUMERIC example" %}
```sql
SELECT ISNUMERIC(transaction_count)
FROM taxi_trips 
LIMIT 1 

-- True
```
{% endcode %}
