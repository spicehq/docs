---
description: >-
  Splits an input string by using a regular expression according to a keyword
  and an integer value.
---

# REGEXP\_SPLIT

### Syntax <a href="#syntax" id="syntax"></a>

#### REGEXP\_SPLIT(_input_ string, _regex_ string, _keyword_ string, _integer_ integer) → array <a href="#regexp_splitinput-string-regex-string-keyword-string-integer-integer--array" id="regexp_splitinput-string-regex-string-keyword-string-integer-integer--array"></a>

* input: The string that you want to split by means of the regular expression.
* regex: The regular expression to use to split the string.
* keyword: The keyword that determines where or how many times to use the regular expression to split the string. Can be FIRST, LAST, INDEX, or ALL.
* integer: The value specified for the keyword.

**Examples**

{% code title="REGEXP_SPLIT example" %}
```sql
SELECT REGEXP_SPLIT('REGULAR AIR', 'R', 'FIRST', -1) 
AS R_LESS_SHIPMENT_TYPE
-- ['', 'EGULAR AIR']
```
{% endcode %}

{% code title="REGEXP_SPLIT example" %}
```sql
SELECT REGEXP_SPLIT('REGULAR AIR', 'R', 'LAST', -1) 
AS R_LESS_SHIPMENT_TYPE
-- ['REGULAR AI', '']
```
{% endcode %}

{% code title="REGEXP_SPLIT example" %}
```sql
SELECT REGEXP_SPLIT('REGULAR AIR', 'R', 'INDEX', 1) 
AS R_LESS_SHIPMENT_TYPE
-- ['REGULA', ' AIR']
```
{% endcode %}

{% code title="REGEXP_SPLIT example" %}
```sql
SELECT REGEXP_SPLIT('REGULAR AIR', 'R', 'ALL', 1) 
AS R_LESS_SHIPMENT_TYPE
-- ['']
```
{% endcode %}

{% code title="REGEXP_SPLIT example" %}
```sql
SELECT REGEXP_SPLIT('REGULAR AIR', 'R', 'ALL', 2) 
AS R_LESS_SHIPMENT_TYPE
-- ['', 'EGULA']
```
{% endcode %}

{% code title="REGEXP_SPLIT example" %}
```sql
SELECT REGEXP_SPLIT('REGULAR AIR', 'R', 'ALL', 3) 
AS R_LESS_SHIPMENT_TYPE
-- ['', 'EGULA', ' AI']
```
{% endcode %}

{% code title="REGEXP_SPLIT example" %}
```sql
SELECT REGEXP_SPLIT('REGULAR AIR', 'R', 'ALL', 4) 
AS R_LESS_SHIPMENT_TYPE
-- ['', 'EGULA', ' AI', '']
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

You can choose from these keywords:

FIRST Specifies to split the string at the first match of the regular expression. The integer value is not used and must always be -1.&#x20;

LAST Specifies to split the string at the last match of the regular expression. The integer value is not used and must always be -1.&#x20;

INDEX Specifies to start finding matches for the regular expression at the specified character position (indicated by the integer value) in the string. Then, split the string at every subsequent match of the regular expression.&#x20;

ALL Specifies to split the string as many times as indicated by the integer value. All subsequent matches of the regular expression are ignored.
