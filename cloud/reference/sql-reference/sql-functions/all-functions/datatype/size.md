---
description: Returns the number of entries in a map expression.
---

# SIZE

### Syntax <a href="#syntax" id="syntax"></a>

#### SIZE(_input_ map) → int <a href="#sizeinput-map--int" id="sizeinput-map--int"></a>

* input: A map expression for which to return the number of entries.

**Examples**

{% code title="SIZE example" %}
```sql
SELECT SIZE(properties)
-- 3
-- 2
```
{% endcode %}
