---
description: Converts the input expression to a binary value.
---

# BINARY\_STRING

### Syntax <a href="#syntax" id="syntax"></a>

#### BINARY\_STRING(_expression_ VARCHAR) → BINARY <a href="#binary_stringexpression-varchar--binary" id="binary_stringexpression-varchar--binary"></a>

* expression: Varchar expression to convert to binary.

**Examples**

{% code title="BINARY_STRING example" %}
```sql
SELECT BINARY_STRING('SPICE')
-- RFJFTUlP
```
{% endcode %}

{% code title="BINARY_STRING example" %}
```sql
SELECT BINARY_STRING('000')
-- MDAw
```
{% endcode %}
