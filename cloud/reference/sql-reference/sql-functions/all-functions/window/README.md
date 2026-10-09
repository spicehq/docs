# Window

| Function Name                              | Description                                                                                                                                                                                                                                                                  |
| ------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [COUNT](../aggregate/count.md)             | Returns the total number of records for the specified expression.                                                                                                                                                                                                            |
| [COVAR\_POP](../aggregate/covar\_pop.md)   | Returns the population covariance for non-NULL pairs across all input values.                                                                                                                                                                                                |
| [COVAR\_SAMP](../aggregate/covar\_samp.md) | Returns the sample covariance for non-NULL pairs across all input values.                                                                                                                                                                                                    |
| [CUME\_DIST](cume\_dist.md)                | Returns the cumulative distribution of the current row with regard to other values within the same window partition.                                                                                                                                                         |
| [DENSE\_RANK](dense\_rank.md)              | Returns the rank of the current row within its partition and ordering. Rows that are equal will have the same rank.                                                                                                                                                          |
| [FIRST\_VALUE](first\_value.md)            | Returns the first value within an ordered group of a result set.                                                                                                                                                                                                             |
| [HLL](../aggregate/hll.md)                 | Uses HyperLogLog to return an approximation of the distinct cardinality of the input.                                                                                                                                                                                        |
| [LAG](lag.md)                              | Returns the row before the current one in a partition based on the `ORDER BY` clause without the need for a self-join. If there are no rows, this function returns `NULL`.                                                                                                   |
| [LEAD](lead.md)                            | Returns the row after the current one in the same result set without the need for a self-join. If there are no rows, this function returns `NULL`.                                                                                                                           |
| [MAX](max.md)                              | Returns the maximum value among the non-NULL input expressions.                                                                                                                                                                                                              |
| [MIN](min.md)                              | Returns the minimum value among the non-NULL input expressions.                                                                                                                                                                                                              |
| [NDV](../aggregate/ndv.md)                 | Returns an approximate distinct value number, similar to `COUNT(DISTINCT col)`. NDV can return results faster than using the combination of COUNT and DISTINCT while using a constant amount of memory, resulting in less memory usage for columns with high cardinality.    |
| [NTILE](ntile.md)                          | Equally splits the rows in each partition into ranked parts specified by the integer value and starting from 1. This function requires the `ORDER BY` clause.                                                                                                                |
| [PERCENT\_RANK](percent\_rank.md)          | Returns the relative rank of the current row in the partition based on the `ORDER BY` clause. The displayed percentage ranges from 0.0 to 1.0.                                                                                                                               |
| [RANK](rank.md)                            | Returns the rank of the current row within its partition and placement order. Rows that are equal have the same rank. However, the count of tied rows is added to the next rank, instead of being incremented by one. The rank value starts at 1 and increases sequentially. |
| [ROW\_NUMBER](row\_number.md)              | Returns the row number for the current row based on the `ORDER BY` clause within each partition. Rows containing identical values receive different row numbers.                                                                                                             |
| [SUM](sum.md)                              | Returns the sum of non-NULL input expressions.                                                                                                                                                                                                                               |
| [VAR\_POP](../aggregate/var\_pop.md)       | Returns the population variance of non-NULL records.                                                                                                                                                                                                                         |
| [VAR\_SAMP](../aggregate/var\_samp.md)     | Returns the sample variance of non-NULL records.                                                                                                                                                                                                                             |

### Window Function Syntax <a href="#window-function-syntax" id="window-function-syntax"></a>

A window function performs a calculation across a set of table rows that has some relationship to the current row. This is comparable to how an aggregate function can run a calculation. The difference is that a window function does not group rows into a single output row. With a window function, the rows retain their separate identities.

A window function call uses the `OVER()` clause directly following the window function’s name and argument(s). The `OVER()` clause may use the following optional arguments:

* `PARTITION BY`: Defines multiple window partitions.
* `ORDER BY`: Orders rows within each partition.

**Syntax**

```sql
window_function (expression) OVER (
   [ PARTITION BY expressionlist ]
   [ ORDER BY fieldlist ] ) 
```

### Aggregate Window Functions <a href="#aggregate-window-functions" id="aggregate-window-functions"></a>

The `OVER()` clause can be used with regular aggregate functions such as:

* AVG
* COUNT
* MAX
* MIN
* SUM

**Example**

The following example uses the sample table provided below to show the `OVER()` clause used with the `SUM` aggregate function.

```sql
select 
   product_id, 
   branch, 
   amount, 
   SUM(amount) OVER (partition by branch order by amount DESC) as total_branch_amount
from transactions
```

| product\_id | branch | amount | total\_branch\_amount |
| ----------- | ------ | ------ | --------------------- |
| Product1    | A      | 30.0   | 30.0                  |
| Product2    | A      | 24.0   | 54.0                  |
| Product3    | A      | 2.0    | 56.0                  |
| Product3    | B      | 45.0   | 45.0                  |
| Product2    | B      | 10.0   | 55.0                  |
| Product1    | B      | 3.0    | 58.0                  |

### General-Purpose Window Functions <a href="#general-purpose-window-functions" id="general-purpose-window-functions"></a>

The `OVER()` clause can be used with the following functions:

<table><thead><tr><th width="210.33333333333331">Function</th><th width="235">Return Type</th><th>Description</th></tr></thead><tbody><tr><td>CUME_DIST()</td><td>Double</td><td>Calculates the cumulative distribution of the current row within the window partition.</td></tr><tr><td>DENSE_RANK()</td><td>BIGINT</td><td>Returns the rank of the current row within its partition and ordering. Rows that are equal have the same rank.</td></tr><tr><td>LAG()</td><td>Same as input</td><td>Returns the row before the current one in a partition. If there are no rows, returns null.</td></tr><tr><td>LEAD()</td><td>Same as input</td><td>Returns the row after the current one in a partition. If there are no rows, returns null.</td></tr><tr><td>NTILE([integer] ntile)</td><td>Integer</td><td>NTILE function equally splits the rows in each partition into N ranked parts. Has to be used with an <code>ORDER BY</code> clause.</td></tr><tr><td>PERCENT_RANK()</td><td>Double</td><td>Returns the percent rank of the current row in the partition based on the order by clause.</td></tr><tr><td>RANK()</td><td>BIGINT</td><td>Returns the rank of the current row within its partition and ordering. Rows that are equal have the same rank. However, the count of tied rows is added to the next rank, instead of being incremented by just one.</td></tr><tr><td>ROW_NUMBER()</td><td>BIGINT</td><td>Returns the row number for the current row based on the order by clause within each partition.</td></tr></tbody></table>

For more information about Window Functions, see [SQL Window Functions](https://www.sqltutorial.org/sql-window-functions/).

\
