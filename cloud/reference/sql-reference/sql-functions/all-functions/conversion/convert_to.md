---
description: Converts a value to a binary string of a supported data type.
---

# CONVERT\_TO

### Syntax <a href="#syntax" id="syntax"></a>

#### CONVERT\_TO(_expression_ value\_to\_convert, _data\_type_ name\_of\_type) → VARBINARY <a href="#convert_toexpression-value_to_convert-data_type-name_of_type--varbinary" id="convert_toexpression-value_to_convert-data_type-name_of_type--varbinary"></a>

* expression: The value to convert to a binary string.
* data\_type: The data type to use for the conversion to a binary string.

**Examples**

{% code title="CONVERT_TO example" %}
```sql
SELECT CONVERT_TO('this value' ,'UTF8')
-- dGhpcyB2YWx1ZQ==
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

You can convert values to binary strings of these data types:

* BIGINT\_BE
* BIGINT\_HADOOPV
* BIGINT
* BOOLEAN\_BYTE
* DATE\_EPOCH\_BE
* DATE\_EPOCH
* DOUBLE\_BE
* DOUBLE
* EXTENDEDJSON
* FLOAT\_BE
* FLOAT
* INT\_BE
* INT\_HADOOPV
* INT
* JSON
* SIMPLEJSON
* TIME\_EPOCH\_BE
* TIME\_EPOCH
* TIMESTAMP\_EPOCH\_BE
* TIMESTAMP\_EPOCH
* UTF8

\
