# RANK

Returns the rank of the current row within its partition and placement order. Rows that are equal have the same rank. However, the count of tied rows is added to the next rank, instead of being incremented by one. The rank value starts at 1 and increases sequentially.

### Syntax <a href="#syntax" id="syntax"></a>

#### RANK() OVER ( \[PARTITION BY partition\_expression] \[ORDER BY order\_expression]) → bigint <a href="#rank-over--partition-by-partition_expression-order-by-order_expression--bigint" id="rank-over--partition-by-partition_expression-order-by-order_expression--bigint"></a>

* partition\_expression: An optional expression that groups rows into partitions.
* order\_expression: An optional expression that specifies the order of the rows within each partition.

**Examples**

{% code title="RANK example" %}
```sql
SELECT "Category", 
  "Descript", 
  "DayOfWeek",
  RANK() 
    OVER (
      PARTITION BY "Category" 
      ORDER BY "DayOfWeek")
FROM taxi_trips 

-- Category, Descript, DayOfWeek, EXPR$3
-- ARSON, ARSON, Friday, 1 
-- ARSON, ARSON, Monday, 40
```
{% endcode %}
