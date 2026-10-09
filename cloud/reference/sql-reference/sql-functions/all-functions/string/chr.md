---
description: >-
  Converts a Unicode code point into the character that matches the input
  Unicode character. If an invalid code point is specified, an empty string is
  returned.
---

# CHR

### Syntax <a href="#syntax" id="syntax"></a>

#### CHR(_integer\_expression_ int32) → varchar <a href="#chrinteger_expression-int32--varchar" id="chrinteger_expression-int32--varchar"></a>

* integer\_expression: Unicode code point to convert to character.

**Examples**

{% code title="CHR example" %}
```sql
SELECT CHR(72)
-- H
```
{% endcode %}

{% code title="CHR example" %}
```sql
SELECT CHR(33)
-- None
```
{% endcode %}
