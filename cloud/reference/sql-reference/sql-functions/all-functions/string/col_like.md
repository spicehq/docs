---
description: >-
  Tests whether an expression column matches a pattern column. Comparisons are
  case-sensitive.
---

# COL\_LIKE

### Syntax <a href="#syntax" id="syntax"></a>

#### COL\_LIKE(_expression\_col_ varchar, _pattern\_col_ varchar) → boolean <a href="#col_likeexpression_col-varchar-pattern_col-varchar--boolean" id="col_likeexpression_col-varchar-pattern_col-varchar--boolean"></a>

* expression\_col: A column containing an expression to compare.
* pattern\_col: A column containing the pattern to compare to the expression.

**Examples**

{% code title="COL_LIKE example" %}
```sql
CREATE TABLE $scratch.names (name varchar, pat varchar)
AS VALUES ('john', '%oh%'), ('jacob', '%aco%'), ('bill', '%ob%');

SELECT name 
FROM $scratch.names 
WHERE col_like (name, pat);

-- john
-- jacob
```
{% endcode %}
