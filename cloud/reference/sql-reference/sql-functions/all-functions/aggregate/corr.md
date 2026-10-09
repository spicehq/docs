---
description: >-
  Calculates the Pearson correlation coefficient of the values expression1 and
  expression2. The function name must be enclosed in double quotes (“CORR”).
---

# CORR

### Syntax <a href="#syntax" id="syntax"></a>

#### CORR(_expression1_ numeric, _expression2_ numeric) → double <a href="#correxpression1-numeric-expression2-numeric--double" id="correxpression1-numeric-expression2-numeric--double"></a>

* expression1: An expression that evaluates to a numeric type. This parameter is the dependent value.
* expression2: An expression that evaluates to a numeric type. This parameter is the independent value.

**Examples**

<pre class="language-sql" data-title="CORR example"><code class="lang-sql"><strong>SELECT "CORR"(100, 4)
</strong>-- NaN
</code></pre>

{% code title="CORR example" %}
```sql
SELECT transaction_count,
  "CORR"(gas_used, base_fee_per_gas)
FROM taxi_trips
GROUP BY transaction_count
LIMIT 5

-- 8, 0.38978465691058356
-- 4, 0.4254971384215677
-- 208, NaN
-- 255, -1.0
-- 3, 0.46551948595802983
```
{% endcode %}
