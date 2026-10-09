---
description: >-
  Converts a binary string to a UTF-8 value and replaces all characters that
  cannot be converted to UTF-8 with the specified replacement character.
---

# CONVERT\_REPLACEUTF8

### Syntax <a href="#syntax" id="syntax"></a>

#### CONVERT\_REPLACEUTF8(_binary\_value_ value\_to\_convert, _replacement_ VARCHAR) → VARCHAR <a href="#convert_replaceutf8binary_value-value_to_convert-replacement-varchar--varchar" id="convert_replaceutf8binary_value-value_to_convert-replacement-varchar--varchar"></a>

* binary\_value: The binary string to convert to a string encoded in UTF8.
* replacement: A single character to use as a substitute for each character in the binary string that cannot be converted to UTF8.

**Examples**

{% code title="CONVERT_REPLACEUTF8 example" %}
```sql
SELECT CONVERT_REPLACEUTF8(BINARY_STRING('These characters cannot be converted — \xa0\xa1'), 'b')
-- These characters cannot be converted — bb
```
{% endcode %}
