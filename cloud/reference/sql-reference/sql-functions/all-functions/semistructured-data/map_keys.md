---
description: Returns all keys from a map expression.
---

# MAP\_KEYS

### Syntax <a href="#syntax" id="syntax"></a>

#### MAP\_KEYS(_input_ map) → array of strings <a href="#map_keysinput-map--array-of-strings" id="map_keysinput-map--array-of-strings"></a>

* input: A map expression for which to return an array of keys.

**Examples**

{% code title="MAP_KEYS example" %}
```sql
SELECT MAP_KEYS(properties)
-- ['Cover', 'Publication Year', 'Color']
```
{% endcode %}
