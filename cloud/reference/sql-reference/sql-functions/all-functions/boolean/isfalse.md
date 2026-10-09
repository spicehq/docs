---
description: >-
  Tests whether the input expression is false. If it is, returns a value of
  true.
---

# ISFALSE

### Syntax <a href="#syntax" id="syntax"></a>

#### ISFALSE(_boolean\_expression_ boolean) → boolean <a href="#isfalseboolean_expression-boolean--boolean" id="isfalseboolean_expression-boolean--boolean"></a>

* boolean\_expression: The input expression, which must be of type `BOOLEAN`.

**Examples**

{% code title="Create a table containing one column of boolean data type" %}
```sql
CREATE TABLE $scratch.test_table 
  (
    test_column BOOLEAN
  )

-- true, Table created
```
{% endcode %}

{% code title="Create two rows containing boolean values" %}
```sql
INSERT INTO $scratch.test_table 
VALUES  (true), 
        (false)

-- Fragment, Records, Path, Metadata, Partition, FileSize, IcebergMetadata, fileschema, PartitionData
-- 0_0, 1, s3://<s3-bucket-path>, , 0, <file-size>, <iceberg-metadata>, <file-schema>, null
```
{% endcode %}

{% code title="Run the ISFALSE query" %}
```sql
SELECT ISFALSE(test_column)
FROM $scratch.test_table

-- false 
-- true 
```
{% endcode %}
