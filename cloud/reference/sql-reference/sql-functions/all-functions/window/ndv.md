# NDV

Returns an approximate distinct value number, similar to `COUNT(DISTINCT col)`. NDV can return results faster than using the combination of COUNT and DISTINCT while using a constant amount of memory, resulting in less memory usage for columns with high cardinality.

### Syntax <a href="#syntax" id="syntax"></a>

#### NDV(_expression_ numeric) → bigint <a href="#ndvexpression-numeric--bigint" id="ndvexpression-numeric--bigint"></a>

* expression: Enter an expression to evaluate the number of records. This value can either be a column name or \*.

**Examples**

{% code title="NDV example" %}
```sql
SELECT NDV(column_name)
-- 163
```
{% endcode %}

#### NDV(_expression_ numeric, _scale_ numeric) → bigint <a href="#ndvexpression-numeric-scale-numeric--bigint" id="ndvexpression-numeric-scale-numeric--bigint"></a>

* expression: Enter an expression to evaluate the number of records. This value can either be a column name or \*.
* scale: Optional argument that maps to a precision used by the HyperLogLog (HLL) algorithm based on the mapping formula: `precision = scale +8`. Enter an integer in the range from 1 to 10.

**Examples**

{% code title="NDV example" %}
```sql
SELECT NDV(column_name, 1)
-- 162
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

The NDV function is used internally by the `COMPUTE STATS` statement for computing the number of distinct values in a column. This function might not reflect the precise number of different values in the column, especially if the cardinality is very low or very high. This function accepts the DISTINCT and ALL keywords: `NDV([DISTINCT | ALL] expression)`.
