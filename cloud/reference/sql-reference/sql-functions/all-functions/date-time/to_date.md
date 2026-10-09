---
description: Converts the input expressions to the corresponding date.
---

# TO\_DATE

### Syntax <a href="#syntax" id="syntax"></a>

#### TO\_DATE(_in_ timestamp) → date <a href="#to_datein-timestamp--date" id="to_datein-timestamp--date"></a>

* in: The date is extracted from the timestamp.

**Examples**

{% code title="TO_DATE example" %}
```sql
SELECT TO_DATE(TIMESTAMP '2022-05-17 19:15:00.000')
-- 2022-05-17
```
{% endcode %}

#### TO\_DATE(_numeric\_expression_ int32) → date <a href="#to_datenumeric_expression-int32--date" id="to_datenumeric_expression-int32--date"></a>

* numeric\_expression: A Unix epoch timestamp.

**Examples**

{% code title="TO_DATE example" %}
```sql
SELECT TO_DATE(1640131200)
-- 2021-12-22
```
{% endcode %}

#### TO\_DATE(_numeric\_expression_ float) → date <a href="#to_datenumeric_expression-float--date" id="to_datenumeric_expression-float--date"></a>

* numeric\_expression: A Unix epoch timestamp.

**Examples**

{% code title="TO_DATE example" %}
```sql
SELECT TO_DATE(1665131223.69)
-- 2022-10-07
```
{% endcode %}

#### TO\_DATE(_numeric\_expression_ int64) → date <a href="#to_datenumeric_expression-int64--date" id="to_datenumeric_expression-int64--date"></a>

* numeric\_expression: A Unix epoch timestamp.

**Examples**

{% code title="TO_DATE example" %}
```sql
SELECT TO_DATE(1640131200)
-- 2021-12-22
```
{% endcode %}

#### TO\_DATE(_string\_expression_ varchar, _format_ varchar, _replaceErrorWithNull_ int32) → date <a href="#to_datestring_expression-varchar-format-varchar-replaceerrorwithnull-int32--date" id="to_datestring_expression-varchar-format-varchar-replaceerrorwithnull-int32--date"></a>

* string\_expression: The string from which to extract the date.
* format: String to specify format of the date.
* replaceErrorWithNull: If 0, the function will fail when given malformed input. If 1, the function will return NULL when given the malformed input.

**Examples**

{% code title="TO_DATE example" %}
```sql
SELECT TO_DATE('2022-07-22.23', 'YYYY-MM-DD', 1)
-- NULL
```
{% endcode %}

#### TO\_DATE(_string\_expression_ varchar, _format_ varchar) → date <a href="#to_datestring_expression-varchar-format-varchar--date" id="to_datestring_expression-varchar-format-varchar--date"></a>

* string\_expression: String from which to extract the date.
* format: String to specify format of the date.

**Examples**

{% code title="TO_DATE example" %}
```sql
SELECT TO_DATE('05/24/22', 'MM/DD/YY')
-- 2022-05-24
```
{% endcode %}

#### TO\_DATE(_numeric\_expression_ double) → date <a href="#to_datenumeric_expression-double--date" id="to_datenumeric_expression-double--date"></a>

* numeric\_expression: A Unix epoch timestamp.

**Examples**

{% code title="TO_DATE example" %}
```sql
SELECT TO_DATE(1665131223.69)
-- 2022-10-07
```
{% endcode %}
