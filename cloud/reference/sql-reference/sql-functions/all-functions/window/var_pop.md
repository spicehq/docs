---
description: Returns the population variance of non-NULL records.
---

# VAR\_POP

### Syntax <a href="#syntax" id="syntax"></a>

#### VAR\_POP(_numeric\_expression_ int64) → double <a href="#var_popnumeric_expression-int64--double" id="var_popnumeric_expression-int64--double"></a>

* numeric\_expression: The set of records to calculate variance for.

**Examples**

{% code title="VAR_POP example" %}
```sql
SELECT VAR_POP(pop) 
FROM taxi_trips
-- 1.5167869917122573E8
```
{% endcode %}

#### VAR\_POP(_numeric\_expression_ double) → double <a href="#var_popnumeric_expression-double--double" id="var_popnumeric_expression-double--double"></a>

* numeric\_expression: The set of records to calculate variance for.

**Examples**

{% code title="VAR_POP example" %}
```sql
SELECT VAR_POP(gas_used) 
FROM taxi_trips
-- 181.26187022731304
```
{% endcode %}

#### VAR\_POP(_numeric\_expression_ float) → double <a href="#var_popnumeric_expression-float--double" id="var_popnumeric_expression-float--double"></a>

* numeric\_expression: The set of records to calculate variance for.

**Examples**

{% code title="VAR_POP example" %}
```sql
SELECT VAR_POP(gas_used) 
FROM taxi_trips
-- 181.26187022731304
```
{% endcode %}

#### VAR\_POP(_numeric\_expression_ int32) → double <a href="#var_popnumeric_expression-int32--double" id="var_popnumeric_expression-int32--double"></a>

* numeric\_expression: The set of records to calculate variance for.

**Examples**

{% code title="VAR_POP example" %}
```sql
SELECT VAR_POP(pop) 
FROM taxi_trips
-- 1.5167869917122573E8
```
{% endcode %}
