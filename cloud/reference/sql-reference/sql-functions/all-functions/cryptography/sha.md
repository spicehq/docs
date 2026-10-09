---
description: Computes the SHA-1 hash value of a string.
---

# SHA

### Syntax <a href="#syntax" id="syntax"></a>

#### SHA(_expr_ varchar) → varchar <a href="#shaexpr-varchar--varchar" id="shaexpr-varchar--varchar"></a>

* expression: The string to hash.

**Examples**

{% code title="SHA example" %}
```sql
SELECT SHA('Spice')
-- dda3f1ef53d1e82a4845ef5b2893b9d9c04bd3b1
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

This function is an alias for [`SHA1`](sha1.md)`.`
