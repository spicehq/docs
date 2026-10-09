---
description: Computes the MD5 hash value of a string.
---

# MD5

### Syntax <a href="#syntax" id="syntax"></a>

#### MD5(_expr_ varchar) → varchar <a href="#md5expr-varchar--varchar" id="md5expr-varchar--varchar"></a>

* expression: The string to hash.

**Examples**

{% code title="MD5 example" %}
```sql
SELECT MD5('Spice')
-- 288e0e9ab8b8ac8737afefecf16f61fd
```
{% endcode %}
