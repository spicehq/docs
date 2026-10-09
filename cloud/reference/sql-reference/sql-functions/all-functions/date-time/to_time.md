---
description: Converts the input expressions to the corresponding time.
---

# TO\_TIME

### Syntax <a href="#syntax" id="syntax"></a>

#### TO\_TIME(_numeric\_expression_ int32) → time <a href="#to_timenumeric_expression-int32--time" id="to_timenumeric_expression-int32--time"></a>

* numeric\_expression: A Unix epoch timestamp.

**Examples**

{% code title="TO_TIME example" %}
```sql
SELECT TO_TIME(1665131223)
-- 08:27:03
```
{% endcode %}

#### TO\_TIME(_numeric\_expression_ int64) → time <a href="#to_timenumeric_expression-int64--time" id="to_timenumeric_expression-int64--time"></a>

* numeric\_expression: A Unix epoch timestamp.

**Examples**

{% code title="TO_TIME example" %}
```sql
SELECT TO_TIME(1665131223)
-- 08:27:03
```
{% endcode %}

#### TO\_TIME(_string\_expression_ varchar, _format_ varchar, _replaceErrorWithNull_ int32) → time <a href="#to_timestring_expression-varchar-format-varchar-replaceerrorwithnull-int32--time" id="to_timestring_expression-varchar-format-varchar-replaceerrorwithnull-int32--time"></a>

* string\_expression: The string from which to extract the time.
* format: String to specify format of the time.
* replaceErrorWithNull: If 0, the function will fail when given malformed input. If 1, the function will return NULL when given malformed input.

**Examples**

{% code title="TO_TIME example" %}
```sql
SELECT TO_TIME('09:15:00.23232', 'HH:MI:SS', 1)
-- NULL
```
{% endcode %}

#### TO\_TIME(_string\_expression_ varchar, _format_ varchar) → time <a href="#to_timestring_expression-varchar-format-varchar--time" id="to_timestring_expression-varchar-format-varchar--time"></a>

* string\_expression: The string from which to extract the time.
* format: String to specify format of the time.

**Examples**

{% code title="TO_TIME example" %}
```sql
SELECT TO_TIME('09:15:00', 'HH:MI:SS')
-- 09:15:00
```
{% endcode %}

#### TO\_TIME(_numeric\_expression_ double) → time <a href="#to_timenumeric_expression-double--time" id="to_timenumeric_expression-double--time"></a>

* numeric\_expression: A Unix epoch timestamp.

**Examples**

{% code title="TO_TIME example" %}
```sql
SELECT TO_TIME(1665131223.69)
-- 08:27:03
```
{% endcode %}

#### TO\_TIME(_numeric\_expression_ float) → time <a href="#to_timenumeric_expression-float--time" id="to_timenumeric_expression-float--time"></a>

* numeric\_expression: A Unix epoch timestamp.

**Examples**

{% code title="TO_TIME example" %}
```sql
SELECT TO_TIME(1665131223.69)
-- 08:27:03
```
{% endcode %}
