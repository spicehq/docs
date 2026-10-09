---
description: Decodes a Base64-encoded string.
---

# UNBASE64

### Syntax <a href="#syntax" id="syntax"></a>

#### UNBASE64(_expression_ varchar) → varbinary <a href="#unbase64expression-varchar--varbinary" id="unbase64expression-varchar--varbinary"></a>

* expression: A Base64-encoded string.

**Examples**

{% code title="UNBASE64 example" %}
```sql
SELECT CAST(UNBASE64('RHJlbWlv') AS VARCHAR)
-- Spice
```
{% endcode %}
