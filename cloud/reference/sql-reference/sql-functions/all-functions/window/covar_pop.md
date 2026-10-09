---
description: Returns the population covariance for non-NULL pairs across all input values.
---

# COVAR\_POP

### Syntax <a href="#syntax" id="syntax"></a>

#### COVAR\_POP(_expression1_ numeric, _expression2_ numeric) → double <a href="#covar_popexpression1-numeric-expression2-numeric--double" id="covar_popexpression1-numeric-expression2-numeric--double"></a>

* expression1: An expression that evaluates to a numeric type. This parameter is the dependent value.
* expression2: An expression that evaluates to a numeric type. This parameter is the independent value.

**Examples**

{% code title="Aggregate function example" %}
```sql
SELECT COVAR_POP(transaction_count, gas_used)
FROM taxi_trips

-- 31.70536771189994
```
{% endcode %}

{% code title="Aggregate function example using optional DISTINCT clause" %}
```sql
SELECT COVAR_POP(DISTINCT transaction_count, gas_used)
FROM taxi_trips

-- 302.592806814443e
```
{% endcode %}

{% code title="Window function example" %}
```sql
SELECT COVAR_POP(transaction_count, gas_used)
  OVER (PARTITION BY transaction_count)
FROM taxi_trips

-- 1.5543122344752192e-15
```
{% endcode %}

### Usage Note <a href="#usage-notes" id="usage-notes"></a>

This function supports the use of `ALL` and `DISTINCT`:

`SELECT COVAR_POP( [ { ALL | DISTINCT } ] expression1, expression2)`
