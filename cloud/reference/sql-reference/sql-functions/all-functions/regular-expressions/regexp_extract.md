---
description: >-
  Extracts the first string in expression that matches the REGEXP expression and
  corresponds to the REGEX group index.
---

# REGEXP\_EXTRACT

### Syntax <a href="#syntax" id="syntax"></a>

#### REGEXP\_EXTRACT(_input_ string, _regex_ string, _expr\_group\_index_ integer) → string <a href="#regexp_extractinput-string-regex-string-expr_group_index-integer--string" id="regexp_extractinput-string-regex-string-expr_group_index-integer--string"></a>

* input: The expression to search for a matching string.
* regex: The Perl-compatible regular expression (PCRE) to match against.
* expr\_group\_index: A regular expression group number, defining which portion of the matching string will be returned.

**Examples**

{% code title="REGEXP_EXTRACT example" %}
```sql
SELECT REGEXP_EXTRACT('100-500', '(\d+)-(\d+)', 1)
-- EXPR$0
  -- 100
```
{% endcode %}

{% code title="REGEXP_EXTRACT example" %}
```sql
SELECT REGEXP_EXTRACT('100-500', '(\d+)-(\d+)', 0)
-- EXPR$0
  -- 100-500
```
{% endcode %}
