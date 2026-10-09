---
description: Returns the sample variance of non-NULL records.
---

# VAR\_SAMP

### Syntax <a href="#syntax" id="syntax"></a>

#### VAR\_SAMP(_numeric\_expression_ int64) → double <a href="#var_sampnumeric_expression-int64--double" id="var_sampnumeric_expression-int64--double"></a>

* numeric\_expression: The set of records to calculate variance for.

**Examples**

{% code title="VAR_SAMP example" %}
```sql
SELECT VAR_SAMP(transaction_count) 
FROM taxi_trips
-- 1.868747683518558
```
{% endcode %}

#### VAR\_SAMP(_numeric\_expression_ float) → double <a href="#var_sampnumeric_expression-float--double" id="var_sampnumeric_expression-float--double"></a>

* numeric\_expression: The set of records to calculate variance for.

**Examples**

{% code title="VAR_SAMP example" %}
```sql
SELECT VAR_SAMP(gas_used) 
FROM taxi_trips
-- 5.106086065187043
```
{% endcode %}

#### VAR\_SAMP(_numeric\_expression_ double) → double <a href="#var_sampnumeric_expression-double--double" id="var_sampnumeric_expression-double--double"></a>

* numeric\_expression: The set of records to calculate variance for.

**Examples**

{% code title="VAR_SAMP example" %}
```sql
SELECT VAR_SAMP(gas_used) 
FROM taxi_trips
-- 5.106086065187043
```
{% endcode %}

#### VAR\_SAMP(_numeric\_expression_ int32) → double <a href="#var_sampnumeric_expression-int32--double" id="var_sampnumeric_expression-int32--double"></a>

* numeric\_expression: The set of records to calculate variance for.

**Examples**

{% code title="VAR_SAMP example" %}
```sql
SELECT VAR_SAMP(transaction_count) 
FROM taxi_trips
-- 1.868747683518558
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

For single input records, VAR\_SAMP will return NULL.
