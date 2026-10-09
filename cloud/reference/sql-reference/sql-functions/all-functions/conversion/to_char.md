---
description: >-
  Converts the input expression to a character/string using the specified
  format.
---

# TO\_CHAR

### Syntax <a href="#syntax" id="syntax"></a>

#### TO\_CHAR(_expression_ time, _format_ varchar) → varchar <a href="#to_charexpression-time-format-varchar--varchar" id="to_charexpression-time-format-varchar--varchar"></a>

* expression: Expression to convert to a string.
* format: Format to use for the conversion.

**Examples**

{% code title="TO_CHAR example" %}
```sql
SELECT TO_CHAR(CAST('01:02:03' AS TIME) , 'HH:MI');
-- 01:02
```
{% endcode %}

#### TO\_CHAR(_expression_ date, _format_ varchar) → varchar <a href="#to_charexpression-date-format-varchar--varchar" id="to_charexpression-date-format-varchar--varchar"></a>

* expression: Expression to convert to a string.
* format: Format to use for the conversion.

**Examples**

{% code title="TO_CHAR example" %}
```sql
SELECT TO_CHAR(CAST('2021-02-11' AS DATE) , 'yyyy.mm.dd');
-- 2021.02.11
```
{% endcode %}

#### TO\_CHAR(_expression_ int32, _format_ varchar) → varchar <a href="#to_charexpression-int32-format-varchar--varchar" id="to_charexpression-int32-format-varchar--varchar"></a>

* expression: Expression to convert to a string.
* format: Format to use for the conversion.

**Examples**

{% code title="TO_CHAR example" %}
```sql
SELECT TO_CHAR(10, '#')
-- 10
```
{% endcode %}

#### TO\_CHAR(_expression_ float, _format_ varchar) → varchar <a href="#to_charexpression-float-format-varchar--varchar" id="to_charexpression-float-format-varchar--varchar"></a>

* expression: Expression to convert to a string.
* format: Format to use for the conversion.

**Examples**

{% code title="TO_CHAR example" %}
```sql
SELECT TO_CHAR(7.5, '#.#')
-- 7.5
```
{% endcode %}

#### TO\_CHAR(_expression_ int64, _format_ varchar) → varchar <a href="#to_charexpression-int64-format-varchar--varchar" id="to_charexpression-int64-format-varchar--varchar"></a>

* expression: Expression to convert to a string.
* format: Format to use for the conversion.

**Examples**

{% code title="TO_CHAR example" %}
```sql
SELECT TO_CHAR(10, '#')
-- 10
```
{% endcode %}

#### TO\_CHAR(_expression_ double, _format_ varchar) → varchar <a href="#to_charexpression-double-format-varchar--varchar" id="to_charexpression-double-format-varchar--varchar"></a>

* expression: Expression to convert to a string.
* format: Format to use for the conversion.

**Examples**

{% code title="TO_CHAR example" %}
```sql
SELECT TO_CHAR(7.5, '#.#')
-- 7.5
```
{% endcode %}

#### TO\_CHAR(_expression_ timestamp, _format_ varchar) → varchar <a href="#to_charexpression-timestamp-format-varchar--varchar" id="to_charexpression-timestamp-format-varchar--varchar"></a>

* expression: Expression to convert to a string.
* format: Format to use for the conversion.

**Examples**

{% code title="TO_CHAR example" %}
```sql
SELECT TO_CHAR(CAST('2013-04-05 01:02:03' AS TIMESTAMP) , 'mm/dd/yyyy, hh:mi');
-- 04/05/2013, 01:02
```
{% endcode %}
