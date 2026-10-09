---
description: >-
  Finds strings that match the given regular expression and replaces the strings
  with the given string.
---

# REGEXP\_REPLACE

### Syntax <a href="#syntax" id="syntax"></a>

#### REGEXP\_REPLACE(_input_ string, _regex_ string, _replacement\_string_ string) → string <a href="#regexp_replaceinput-string-regex-string-replacement_string-string--string" id="regexp_replaceinput-string-regex-string-replacement_string-string--string"></a>

* input: The expression to search for a matching string.
* regex: The Perl-compatible regular expression (PCRE) to match against.
* replacement\_string: The string with which to replace the matching string.

**Examples**

{% code title="REGEXP_REPLACE example" %}
```sql
SELECT REGEXP_REPLACE('8AM-4PM', '\Q-\E', ' to ')
-- EXPR$0
  -- 8AM to 4PM
```
{% endcode %}

{% code title="REGEXP_REPLACE example" %}
```sql
SELECT REGEXP_REPLACE(Address, '\QST\E', 'STREET') 
FROM taxi_trips 
LIMIT 3

-- Raw data
  -- 800 Block of BRYANT ST
  -- 800 Block of BRYANT ST
  -- KEITH ST / SHAFTER AV

-- Returned data
  -- 800 Block of BRYANT STREET
  -- 800 Block of BRYANT STREET
  -- KEITH STREET / SHAFTER AV
```
{% endcode %}
