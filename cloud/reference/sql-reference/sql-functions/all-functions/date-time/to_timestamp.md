---
description: Converts the input expressions to the corresponding timestamp.
---

# TO\_TIMESTAMP

### Syntax <a href="#syntax" id="syntax"></a>

#### TO\_TIMESTAMP(_numeric\_expression_ double) → timestamp <a href="#to_timestampnumeric_expression-double--timestamp" id="to_timestampnumeric_expression-double--timestamp"></a>

* numeric\_expression: A Unix epoch timestamp.

**Examples**

{% code title="TO_TIMESTAMP example" %}
```sql
SELECT TO_TIMESTAMP(52 * 365.25 * 86400)
-- 2022-01-01 00:00:00
```
{% endcode %}

#### TO\_TIMESTAMP(_string\_expression_ varchar, _format_ varchar, _replaceErrorWithNull_ int32) → timestamp <a href="#to_timestampstring_expression-varchar-format-varchar-replaceerrorwithnull-int32--timestamp" id="to_timestampstring_expression-varchar-format-varchar-replaceerrorwithnull-int32--timestamp"></a>

* string\_expression: The string from which to extract the timestamp.
* format: String to specify format of the timestamp.
* replaceErrorWithNull: If 0, the function will fail when given malformed input. If 1, the function will return NULL when given malformed input.

**Examples**

{% code title="TO_TIMESTAMP example" %}
```sql
SELECT TO_TIMESTAMP('2022-03-15 01:02:03.1245', 'YYYY-MM-DD HH:MI:SS', 1)
-- NULL
```
{% endcode %}

#### TO\_TIMESTAMP(_numeric\_expression_ int64) → timestamp <a href="#to_timestampnumeric_expression-int64--timestamp" id="to_timestampnumeric_expression-int64--timestamp"></a>

* numeric\_expression: A Unix epoch timestamp.

**Examples**

{% code title="TO_TIMESTAMP example" %}
```sql
SELECT TO_TIMESTAMP(1640131200)
-- 2021-12-22 00:00:00
```
{% endcode %}

#### TO\_TIMESTAMP(_numeric\_expression_ int32) → timestamp <a href="#to_timestampnumeric_expression-int32--timestamp" id="to_timestampnumeric_expression-int32--timestamp"></a>

* numeric\_expression: A Unix epoch timestamp.

**Examples**

{% code title="TO_TIMESTAMP example" %}
```sql
SELECT TO_TIMESTAMP(1640131200)
-- 2021-12-22 00:00:00
```
{% endcode %}

#### TO\_TIMESTAMP(_numeric\_expression_ float) → timestamp <a href="#to_timestampnumeric_expression-float--timestamp" id="to_timestampnumeric_expression-float--timestamp"></a>

* numeric\_expression: A Unix epoch timestamp.

**Examples**

{% code title="TO_TIMESTAMP example" %}
```sql
SELECT TO_TIMESTAMP(52 * 365.25 * 86400)
-- 2022-01-01 00:00:00
```
{% endcode %}

#### TO\_TIMESTAMP(_string\_expression_ varchar, _format_ varchar) → timestamp <a href="#to_timestampstring_expression-varchar-format-varchar--timestamp" id="to_timestampstring_expression-varchar-format-varchar--timestamp"></a>

* string\_expression: String from which to extract the timestamp.
* format: String to specify format of the timestamp.

**Examples**

{% code title="TO_TIMESTAMP example" %}
```sql
SELECT TO_TIMESTAMP('2021-07-31 01:02:03', 'YYYY-MM-DD HH:MI:SS')
-- 2021-07-31 01:02:03
```
{% endcode %}
