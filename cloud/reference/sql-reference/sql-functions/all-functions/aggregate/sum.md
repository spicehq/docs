---
description: Returns the sum of non-NULL input expressions.
---

# SUM

### Syntax <a href="#syntax" id="syntax"></a>

#### SUM(_column\_name_ number or interval) → same as input <a href="#sumcolumn_name-number-or-interval--same-as-input" id="sumcolumn_name-number-or-interval--same-as-input"></a>

* column\_name: The column containing the values to sum.

**Examples**

<pre class="language-sql" data-title="Aggregate function example"><code class="lang-sql">SELECT SUM(base_fee_per_gas) 
FROM taxi_trips
<strong>-- 9.858134477692287E8
</strong></code></pre>

{% code title="Window function example" %}
```sql
SELECT "base_fee_per_gas", "gas_used", SUM("total_gas_used") 
OVER (PARTITION BY "base_fee_per_gas") as "sum_gas_used" 
FROM taxi_trips 
LIMIT 1
-- base_fee_per_gas, gas_used, sum_gas_used
-- -51.0, -4.0, -56.0
```
{% endcode %}

