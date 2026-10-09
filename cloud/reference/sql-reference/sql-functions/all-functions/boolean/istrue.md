---
description: Returns TRUE if the input expression evaluates to TRUE.
---

# ISTRUE

### Syntax <a href="#syntax" id="syntax"></a>

#### ISTRUE(_expression_ int64) → boolean <a href="#istrueexpression-int64--boolean" id="istrueexpression-int64--boolean"></a>

* expression: Input expression.

**Examples**

{% code title="ISTRUE example" %}
```sql
SELECT ISTRUE(1)
-- True
```
{% endcode %}

#### ISTRUE(_expression_ boolean) → boolean <a href="#istrueexpression-boolean--boolean" id="istrueexpression-boolean--boolean"></a>

* expression: Input expression.

**Examples**

{% code title="ISTRUE example" %}
```sql
SELECT ISTRUE(FALSE)
-- False
```
{% endcode %}

#### ISTRUE(_expression_ int32) → boolean <a href="#istrueexpression-int32--boolean" id="istrueexpression-int32--boolean"></a>

* expression: Input expression.

**Examples**

{% code title="ISTRUE example" %}
```sql
SELECT ISTRUE(0)
-- False
```
{% endcode %}

\
