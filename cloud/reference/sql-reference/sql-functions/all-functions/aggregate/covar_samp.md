---
description: Returns the sample covariance for non-NULL pairs across all input values.
---

# COVAR\_SAMP

### Syntax <a href="#syntax" id="syntax"></a>

#### COVAR\_SAMP(_expression1_ numeric, _expression2_ numeric) → double <a href="#covar_sampexpression1-numeric-expression2-numeric--double" id="covar_sampexpression1-numeric-expression2-numeric--double"></a>

* expression1: An expression that evaluates to a numeric type. This parameter is the dependent value.
* expression2: An expression that evaluates to a numeric type. This parameter is the independent value.

**Examples**

{% code title="Aggregate function example" %}
```sql
SELECT COVAR_SAMP(transaction_count, gas_used)
FROM taxi_trips

-- 31.70536780565699
```
{% endcode %}

{% code title="Aggregate function example using optional DISTINCT clause" %}
```sql
SELECT COVAR_SAMP(DISTINCT transaction_count, gas_used)
FROM taxi_trips

-- 31.7053678056971
```
{% endcode %}

{% code title="Window function example" %}
```sql
SELECT COVAR_SAMP(transaction_count, gas_used)
  OVER (PARTITION BY transaction_count)
FROM taxi_trips

-- 2.442515362986122e-15
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

This function supports the use of `ALL` and `DISTINCT`:

`SELECT COVAR_SAMP( [ { ALL | DISTINCT } ] expression1, expression2)`
