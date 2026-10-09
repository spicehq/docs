# LOCATE

Searches for the first occurrence of the first argument in the second argument and if found, returns the position the of the first argument in the second argument. The first character in a string is position 1. Returns 0 if the substring isn’t found in the expression.

### Syntax <a href="#syntax" id="syntax"></a>

#### LOCATE(_substring_ varchar, _expression_ varchar, _start_ int32) → int32 <a href="#locatesubstring-varchar-expression-varchar-start-int32--int32" id="locatesubstring-varchar-expression-varchar-start-int32--int32"></a>

* substring: Substring to search for in the expression.
* expression: The input expression to search.
* start: Position to start the search from.

**Examples**

{% code title="LOCATE example" %}
```sql
SELECT LOCATE('an','banana', 3)
-- 4
```
{% endcode %}

{% code title="LOCATE example" %}
```sql
SELECT LOCATE('no','banana')
-- 0
```
{% endcode %}

#### LOCATE(_substring_ varchar, _expression_ varchar) → int32 <a href="#locatesubstring-varchar-expression-varchar--int32" id="locatesubstring-varchar-expression-varchar--int32"></a>

* substring: Substring to search for in the expression.
* expression: The input expression to search.

**Examples**

{% code title="LOCATE example" %}
```sql
SELECT LOCATE('an','banana')
-- 2
```
{% endcode %}
