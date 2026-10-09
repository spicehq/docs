---
description: Returns a hexadecimal string for the given binary value.
---

# TO\_HEX

### Syntax <a href="#syntax" id="syntax"></a>

#### TO\_HEX(_in_ binary) → string <a href="#to_hexin-binary--string" id="to_hexin-binary--string"></a>

* in: A binary value.

**Examples**

{% code title="TO_HEX example" %}
```sql
SELECT to_hex(binary_string('hello'))
-- 68656C6C6F
```
{% endcode %}
