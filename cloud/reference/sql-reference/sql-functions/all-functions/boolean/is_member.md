---
description: Returns whether the current user is a member of the specified role.
---

# IS\_MEMBER

### Syntax <a href="#syntax" id="syntax"></a>

#### IS\_MEMBER(_expression_ varchar) → boolean <a href="#is_memberexpression-varchar--boolean" id="is_memberexpression-varchar--boolean"></a>

* expression: String expression identifying a role in Spice.

**Examples**

{% code title="IS_MEMBER example" %}
```sql
SELECT IS_MEMBER ('public')
-- True
```
{% endcode %}

{% code title="IS_MEMBER example" %}
```sql
SELECT IS_MEMBER ('non-role')
-- False
```
{% endcode %}
