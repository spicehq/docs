---
description: >-
  Returns a consistent hash value based on the input string. This function
  returns NULL for non-string types.
---

# MASK\_HASH

### Syntax <a href="#syntax" id="syntax"></a>

#### MASK\_HASH(_expression_ varchar) → varchar <a href="#mask_hashexpression-varchar--varchar" id="mask_hashexpression-varchar--varchar"></a>

* expression: The string to hash.

**Examples**

{% code title="MASK_HASH example" %}
```sql
SELECT MASK_HASH('abcd-ABCD-1234')
-- 770d599256e3902a0aacc9750cd1ca7f34be182632ba3dca3d2eb6f31dcc3d59
```
{% endcode %}
