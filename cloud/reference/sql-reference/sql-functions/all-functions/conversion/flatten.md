---
description: >-
  Explodes compound values into multiple rows. This function takes a LIST column
  and produces a lateral view (that is, an inline view that contains correlation
  referring to other tables that precede it
---

# FLATTEN

### Syntax <a href="#syntax" id="syntax"></a>

#### FLATTEN(_expression_ list) → list <a href="#flattenexpression-list--list" id="flattenexpression-list--list"></a>

* expression: The expression that will be unpacked into rows. The expression must be of data type `LIST`.

**Examples**

{% code title="FLATTEN example" %}
```sql
SELECT FLATTEN(CONVERT_FROM ('["Ford", "BMW", "Fiat"]', 'json'))
-- Ford
-- BMW
-- Fiat
```
{% endcode %}
