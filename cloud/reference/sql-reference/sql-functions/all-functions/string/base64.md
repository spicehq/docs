---
description: Returns the Base64 encoding of a binary string.
---

# BASE64

### Syntax <a href="#syntax" id="syntax"></a>

#### BASE64(_expression_ varbinary) → varchar <a href="#base64expression-varbinary--varchar" id="base64expression-varbinary--varchar"></a>

* expression: The string to encode.

**Examples**

{% code title="BASE64 example" %}
```sql
SELECT BASE64('Spice')
-- RHJlbWlv
```
{% endcode %}
