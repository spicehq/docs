---
description: Converts the hexadecimal number into the bytes represented by a number.
---

# UNHEX

### Syntax <a href="#syntax" id="syntax"></a>

#### UNHEX(_expression_ varchar) → varbinary <a href="#unhexexpression-varchar--varbinary" id="unhexexpression-varchar--varbinary"></a>

* expression: A string containing only hexadecimal digits.

**Examples**

{% code title="UNHEX example" %}
```sql
SELECT UNHEX('4472656D696F')
-- RHJlbWlv
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

The returned value is a binary string. If the expression contains non-hex characters, the result is `NULL`.
