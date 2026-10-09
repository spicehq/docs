---
description: Computes the SHA-1 hash value of a string.
---

# SHA1

### Syntax <a href="#syntax" id="syntax"></a>

#### SHA1(_expr_ varchar) → varchar <a href="#sha1expr-varchar--varchar" id="sha1expr-varchar--varchar"></a>

* expression: The string to hash.

**Examples**

{% code title="SHA1 example" %}
```sql
SELECT SHA1('Spice')
-- dda3f1ef53d1e82a4845ef5b2893b9d9c04bd3b1
```
{% endcode %}
