---
description: >-
  Returns the population standard deviation (square root of variance) of
  non-NULL values in a column with a numeric data type. If all records inside a
  group are NULL, returns NULL.
---

# STDDEV\_POP

### Syntax <a href="#syntax" id="syntax"></a>

#### STDDEV\_POP(_numeric\_expression_ double) → double <a href="#stddev_popnumeric_expression-double--double" id="stddev_popnumeric_expression-double--double"></a>

* numeric\_expression: The set of records to calculate the population standard deviation for.

**Examples**

{% code title="STDDEV_POP example" %}
```sql
SELECT STDDEV_POP(gas_used) 
FROM taxi_trips
-- 2.259665030506379
```
{% endcode %}

#### STDDEV\_POP(_numeric\_expression_ float) → double <a href="#stddev_popnumeric_expression-float--double" id="stddev_popnumeric_expression-float--double"></a>

* numeric\_expression: The set of records to calculate the population standard deviation for.

**Examples**

{% code title="STDDEV_POP example" %}
```sql
SELECT STDDEV_POP(gas_used) 
FROM taxi_trips
-- 2.259665030506379
```
{% endcode %}

#### STDDEV\_POP(_numeric\_expression_ int32) → double <a href="#stddev_popnumeric_expression-int32--double" id="stddev_popnumeric_expression-int32--double"></a>

* numeric\_expression: The set of records to calculate the population standard deviation for.

**Examples**

{% code title="STDDEV_POP example" %}
```sql
SELECT STDDEV_POP(transaction_count) 
FROM taxi_trips
-- 1.367021462155777
```
{% endcode %}

#### STDDEV\_POP(_numeric\_expression_ int64) → double <a href="#stddev_popnumeric_expression-int64--double" id="stddev_popnumeric_expression-int64--double"></a>

* numeric\_expression: The set of records to calculate the population standard deviation for.

**Examples**

{% code title="STDDEV_POP example" %}
```sql
SELECT STDDEV_POP(transaction_count) 
FROM taxi_trips
-- 1.367021462155777
```
{% endcode %}
