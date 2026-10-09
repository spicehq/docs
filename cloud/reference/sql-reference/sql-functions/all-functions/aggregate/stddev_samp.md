---
description: >-
  Returns the sample standard deviation (square root of sample variance) of
  non-NULL values in a column with a numeric data type. If all records inside a
  group are NULL, returns NULL.
---

# STDDEV\_SAMP

### Syntax <a href="#syntax" id="syntax"></a>

#### STDDEV\_SAMP(_numeric\_expression_ double) → double <a href="#stddev_sampnumeric_expression-double--double" id="stddev_sampnumeric_expression-double--double"></a>

* numeric\_expression: The set of records to calculate the sample standard deviation for.

**Examples**

{% code title="STDDEV_SAMP example" %}
```sql
SELECT STDDEV_SAMP(gas_used) 
FROM taxi_trips
-- 2.259665033869644
```
{% endcode %}

#### STDDEV\_SAMP(_numeric\_expression_ float) → double <a href="#stddev_sampnumeric_expression-float--double" id="stddev_sampnumeric_expression-float--double"></a>

* numeric\_expression: The set of records to calculate the sample standard deviation for.

**Examples**

{% code title="STDDEV_SAMP example" %}
```sql
SELECT STDDEV_SAMP(gas_used) 
FROM taxi_trips
-- 2.259665033869644
```
{% endcode %}

#### STDDEV\_SAMP(_numeric\_expression_ int32) → double <a href="#stddev_sampnumeric_expression-int32--double" id="stddev_sampnumeric_expression-int32--double"></a>

* numeric\_expression: The set of records to calculate the sample standard deviation for.

**Examples**

{% code title="STDDEV_SAMP example" %}
```sql
SELECT STDDEV_SAMP(transaction_count) 
FROM taxi_trips
-- 1.3670214641762426
```
{% endcode %}

#### STDDEV\_SAMP(_numeric\_expression_ int64) → double <a href="#stddev_sampnumeric_expression-int64--double" id="stddev_sampnumeric_expression-int64--double"></a>

* numeric\_expression: The set of records to calculate the sample standard deviation for.

**Examples**

{% code title="STDDEV_SAMP example" %}
```sql
SELECT STDDEV_SAMP(transaction_count) 
FROM taxi_trips
-- 1.3670214641762426
```
{% endcode %}
