---
description: Returns the length of the string in bytes.
---

# OCTET\_LENGTH

### Syntax <a href="#syntax" id="syntax"></a>

#### OCTET\_LENGTH(_input_ varchar) → int32 <a href="#octet_lengthinput-varchar--int32" id="octet_lengthinput-varchar--int32"></a>

* input: The string for which the length is returned.

**Examples**

{% code title="OCTET_LENGTH example" %}
```sql
SELECT OCTET_LENGTH('abc')
-- 3
```
{% endcode %}
