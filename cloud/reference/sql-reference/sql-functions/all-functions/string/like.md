---
description: >-
  Tests whether an expression matches one or more patterns. Comparisons are
  case-sensitive.
---

# LIKE

### Syntax <a href="#syntax" id="syntax"></a>

#### _expression_ \[ NOT ] LIKE _pattern_ → boolean <a href="#expression--not--like-pattern--boolean" id="expression--not--like-pattern--boolean"></a>

* expression: The expression to compare.
* NOT: A keyword that inverts the return value.
* pattern: The pattern that is compared to the expression.

**Examples**

{% code title="LIKE example" %}
```sql
SELECT 'pancake' LIKE '%cake'
-- True
```
{% endcode %}

{% code title="LIKE example" %}
```sql
SELECT 'pancake' NOT LIKE '%cake'
-- False
```
{% endcode %}

#### _expression_ \[ NOT ] LIKE _pattern_ ESCAPE _escape\_character_ → boolean <a href="#expression--not--like-pattern-escape-escape_character--boolean" id="expression--not--like-pattern-escape-escape_character--boolean"></a>

* expression: The expression to compare.
* NOT: A keyword that inverts the return value.
* pattern: The pattern that is compared to the expression.
* escape\_character: Putting _escape\_character_ before a wildcard in _pattern_ makes LIKE treat the wildcard as a regular character when it appears in _expression_.

**Examples**

{% code title="LIKE example" %}
```sql
SELECT '50%_Off' LIKE '%50!%%' ESCAPE '!'
-- True
```
{% endcode %}

{% code title="LIKE example" %}
```sql
SELECT '%SalesData%/Users/Jane' NOT LIKE '/%SalesData/%//Users/%' ESCAPE '/'
-- False
```
{% endcode %}

#### _expression_ \[ NOT ] LIKE { ANY | SOME | ALL } ( \[ _pattern_ \[, …] ] ) → boolean <a href="#expression--not--like--any--some--all----pattern------boolean" id="expression--not--like--any--some--all----pattern------boolean"></a>

* expression: A `STRING` expression.
* NOT: A keyword that inverts the return value.
* ANY or SOME or ALL: Keywords indicating whether the expression must match at least one pattern or must match all patterns.
* pattern: One or more `STRING` expressions.

**Examples**

{% code title="LIKE example" %}
```sql
SELECT 'Spark' LIKE ALL ('_park', '%ark')
-- True
```
{% endcode %}

{% code title="LIKE example" %}
```sql
SELECT 'Spark' NOT LIKE ALL ('_park', '%ark')
-- False
```
{% endcode %}

{% code title="LIKE example" %}
```sql
SELECT 'Spark' LIKE SOME ('meow', 'woof')
-- False
```
{% endcode %}

{% code title="LIKE example" %}
```sql
SELECT 'Spark' NOT LIKE SOME ('meow', 'woof')
-- True
```
{% endcode %}

{% code title="LIKE example" %}
```sql
SELECT 'Spark' LIKE ANY ('_park', '_mart')
-- True
```
{% endcode %}

{% code title="LIKE example" %}
```sql
SELECT 'Spark' NOT LIKE ANY ('_park', '_mart')
-- False
```
{% endcode %}
