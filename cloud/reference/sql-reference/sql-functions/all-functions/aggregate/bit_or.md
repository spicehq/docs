---
description: Returns the bitwise OR of non-NULL input values.
---

# BIT\_OR

### Syntax <a href="#syntax" id="syntax"></a>

#### BIT\_OR(_expression_ int) → int <a href="#bit_orexpression-int--int" id="bit_orexpression-int--int"></a>

* expression: An expression that evaluates to a data type that can be cast as an integer.

**Examples**

<pre class="language-sql" data-title="BIT_OR example"><code class="lang-sql"><strong>SELECT BIT_OR(transaction_count)
</strong>  FROM taxi_trips

-- EXPR$0
-- 255
</code></pre>

<pre class="language-sql" data-title="BIT_OR example"><code class="lang-sql"><strong>SELECT gas_used, 
</strong>  BIT_OR(transaction_count)
FROM taxi_trips
GROUP BY gas_used

-- total_amount, EXPR$1
-- 29.0, 7
-- 5.5, 15
</code></pre>

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

If no rows match, returns a value with all bits set to 0. NULL values have no effect on the result unless all results are NULL, which is treated as no match.
