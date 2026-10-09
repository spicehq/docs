---
description: Tests whether the entire expression matches a pattern.
---

# SIMILAR\_TO

### Syntax <a href="#syntax" id="syntax"></a>

#### _expression_ SIMILAR TO _pattern_ → boolean <a href="#expression-similar-to-pattern--boolean" id="expression-similar-to-pattern--boolean"></a>

* expression: The expression to compare.
* pattern: The pattern that is compared to the expression.

**Examples**

{% code title="SIMILAR_TO example" %}
```sql
SELECT 'shortcakes' SIMILAR TO '%cake_'
-- True
```
{% endcode %}

#### _expression_ SIMILAR TO _pattern_ ESCAPE _escape\_character_ → boolean <a href="#expression-similar-to-pattern-escape-escape_character--boolean" id="expression-similar-to-pattern-escape-escape_character--boolean"></a>

* expression: The expression to compare.
* pattern: The pattern that is compared to the expression.
* escape: Putting an _escape\_character_ before a wildcard in the _pattern_ makes SIMILAR TO treat the wildcard as a regular character when it appears in the _expression_.

**Examples**

{% code title="SIMILAR_TO example" %}
```sql
SELECT '100%' SIMILAR TO '100!%' ESCAPE '!'
-- True
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

Succeeds only if the pattern matches the entire expression.
