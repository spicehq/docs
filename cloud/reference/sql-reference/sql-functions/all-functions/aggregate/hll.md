---
description: >-
  Uses HyperLogLog to return an approximation of the distinct cardinality of the
  input.
---

# HLL

### Syntax <a href="#syntax" id="syntax"></a>

#### HLL(_expression_ boolean) → varbinary <a href="#hllexpression-boolean--varbinary" id="hllexpression-boolean--varbinary"></a>

* expression: Expression for which you want to know the number of distinct values.

**Examples**

<pre class="language-sql" data-title="HLL example"><code class="lang-sql"><strong>SELECT HLL(TRUE)
</strong>-- sample return
</code></pre>

#### HLL(_expression_ float) → varbinary <a href="#hllexpression-float--varbinary" id="hllexpression-float--varbinary"></a>

* expression: Expression for which you want to know the number of distinct values.

**Examples**

{% code title="HLL example" %}
```sql
SELECT HLL(67.90)
-- sample return
```
{% endcode %}

#### HLL(_expression_ varbinary) → varbinary <a href="#hllexpression-varbinary--varbinary" id="hllexpression-varbinary--varbinary"></a>

* expression: Expression for which you want to know the number of distinct values.

#### HLL(_expression_ date) → varbinary <a href="#hllexpression-date--varbinary" id="hllexpression-date--varbinary"></a>

* expression: Expression for which you want to know the number of distinct values.

**Examples**

{% code title="HLL example" %}
```sql
SELECT HLL('2021-06-15')
-- sample return
```
{% endcode %}

#### HLL(_expression_ interval\_year) → varbinary <a href="#hllexpression-interval_year--varbinary" id="hllexpression-interval_year--varbinary"></a>

* expression: Expression for which you want to know the number of distinct values.

#### HLL(_expression_ interval\_day) → varbinary <a href="#hllexpression-interval_day--varbinary" id="hllexpression-interval_day--varbinary"></a>

* expression: Expression for which you want to know the number of distinct values.

#### HLL(_expression_ int64) → varbinary <a href="#hllexpression-int64--varbinary" id="hllexpression-int64--varbinary"></a>

* expression: Expression for which you want to know the number of distinct values.

**Examples**

{% code title="HLL example" %}
```sql
SELECT HLL(2021)
-- sample return
```
{% endcode %}

#### HLL(_expression_ double) → varbinary <a href="#hllexpression-double--varbinary" id="hllexpression-double--varbinary"></a>

* expression: Expression for which you want to know the number of distinct values.

**Examples**

{% code title="HLL example" %}
```sql
SELECT HLL(45.7)
-- sample return
```
{% endcode %}

#### HLL(_expression_ time) → varbinary <a href="#hllexpression-time--varbinary" id="hllexpression-time--varbinary"></a>

* expression: Expression for which you want to know the number of distinct values.

#### HLL(_expression_ int32) → varbinary <a href="#hllexpression-int32--varbinary" id="hllexpression-int32--varbinary"></a>

* expression: Expression for which you want to know the number of distinct values.

**Examples**

{% code title="HLL example" %}
```sql
SELECT HLL(2021)
-- sample return 1
```
{% endcode %}

#### HLL(_expression_ varchar) → varbinary <a href="#hllexpression-varchar--varbinary" id="hllexpression-varchar--varbinary"></a>

* expression: Expression for which you want to know the number of distinct values.

**Examples**

{% code title="HLL example" %}
```sql
SELECT HLL('spice')
-- sample return 1
```
{% endcode %}

#### HLL(_expression_ timestamp) → varbinary <a href="#hllexpression-timestamp--varbinary" id="hllexpression-timestamp--varbinary"></a>

* expression: Expression for which you want to know the number of distinct values.

**Examples**

{% code title="HLL example" %}
```sql
SELECT HLL('2021-06-17 11:50:59')
-- sample return 1
```
{% endcode %}
