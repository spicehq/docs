---
description: Returns all values from a map expression.
---

# MAP\_VALUES

### Syntax <a href="#syntax" id="syntax"></a>

#### MAP\_VALUES(_input_ map) → array <a href="#map_valuesinput-map--array" id="map_valuesinput-map--array"></a>

* input: A map expression for which to return an array of values.

**Examples**

{% code title="MAP_VALUES example" %}
```sql
SELECT MAP_VALUES(properties)
-- ['Hardcover', '2002', 'Blue']
```
{% endcode %}
