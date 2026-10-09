---
description: >-
  Determines if an expression is NULL or not NULL. Alias for the function
  ISNULL/ISNOTNULL.
---

# IS \[NOT] NULL

### Syntax <a href="#syntax" id="syntax"></a>

#### IS \[NOT] NULL(_expression_ any) → boolean <a href="#is-not-nullexpression-any--boolean" id="is-not-nullexpression-any--boolean"></a>

* expression: Expression of any Spice supported data type to evaluate.

**Examples**

{% code title="ISNULL function returns true if <expression> is NULL, and false otherwise." %}
```sql
SELECT ISNULL('SPICE')
-- False
```
{% endcode %}

{% code title="ISNULL operator returns true if <expression> is NULL, and false otherwise." %}
```sql
SELECT 'SPICE' IS NULL
-- False
```
{% endcode %}

#### IS \[NOT] NULL(_expression_ any) → boolean <a href="#is-not-nullexpression-any--boolean-1" id="is-not-nullexpression-any--boolean-1"></a>

* expression: Expression of any Spice supported data type to evaluate.

**Examples**

{% code title="ISNOTNULL function returns true if <expression> is not NULL, and false otherwise." %}
```sql
SELECT ISNOTNULL('SPICE')
-- True
```
{% endcode %}

ISNOTNULL operator returns true if \<expression> is not NULL, and false otherwise.

{% code title="ISNOTNULL operator returns true if <expression> is not NULL, and false otherwise." %}
```sql
SELECT 'SPICE' IS NOT NULL
-- False
```
{% endcode %}
