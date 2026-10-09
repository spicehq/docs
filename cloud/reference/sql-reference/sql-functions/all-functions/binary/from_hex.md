---
description: Returns a binary value for the given hexadecimal string.
---

# FROM\_HEX

### Syntax <a href="#syntax" id="syntax"></a>

#### FROM\_HEX(_in_ string) → binary <a href="#from_hexin-string--binary" id="from_hexin-string--binary"></a>

* in: A hexadecimal string.

**Examples**

{% code title="FROM_HEX example" %}
```sql
SELECT from_hex('3fd98a3c')
-- P9mKPA==
```
{% endcode %}

