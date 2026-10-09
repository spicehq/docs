---
description: >-
  Returns the standard deviation of non-NULL values in a column with a numeric
  data type. If all records inside a group are NULL, returns NULL.
---

# STDDEV

### Syntax <a href="#syntax" id="syntax"></a>

#### STDDEV(_numeric\_expression_ double) → double <a href="#stddevnumeric_expression-double--double" id="stddevnumeric_expression-double--double"></a>

* numeric\_expression: The set of records to calculate the standard deviation for.

**Examples**

{% code title="STDDEV example" %}
```sql
SELECT STDDEV(gas_used) 
FROM taxi_trips
-- 2.2596650338461766
```
{% endcode %}

#### STDDEV(_numeric\_expression_ float) → double <a href="#stddevnumeric_expression-float--double" id="stddevnumeric_expression-float--double"></a>

* numeric\_expression: The set of records to calculate the standard deviation for.

**Examples**

{% code title="STDDEV example" %}
```sql
SELECT STDDEV(gas_used) 
FROM taxi_trips
-- 2.2596650338461766
```
{% endcode %}

#### STDDEV(_numeric\_expression_ int32) → double <a href="#stddevnumeric_expression-int32--double" id="stddevnumeric_expression-int32--double"></a>

* numeric\_expression: The set of records to calculate the standard deviation for.

**Examples**

{% code title="STDDEV example" %}
```sql
SELECT STDDEV(transaction_count) 
FROM taxi_trips
-- 1.3670214641762426
```
{% endcode %}

#### STDDEV(_numeric\_expression_ int64) → double <a href="#stddevnumeric_expression-int64--double" id="stddevnumeric_expression-int64--double"></a>

* numeric\_expression: The set of records to calculate the standard deviation for.

**Examples**

{% code title="STDDEV example" %}
```sql
SELECT STDDEV(transaction_count) 
FROM taxi_trips
-- 1.3670214641762426
```
{% endcode %}
