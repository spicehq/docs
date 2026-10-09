---
description: >-
  Returns a hash value of the arguments. HASH does not return NULL, even for
  NULL inputs.
---

# HASH

### Syntax <a href="#syntax" id="syntax"></a>

#### HASH(_expression_ any) → numeric <a href="#hashexpression-any--numeric" id="hashexpression-any--numeric"></a>

* expression: Can be a general expression of any Spice supported data type.

**Examples**

{% code title="HASH example" %}
```sql
SELECT HASH(host_id)
FROM taxi_trips 
LIMIT 5

-- 1110609030
-- 1283762365
-- -1745730253
-- 0
-- 0
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

HASH is a proprietary function that can accept different input expressions of arbitrary Spice supported data types and returns a signed value. It is not a cryptographic hash function and should not be used as such.
