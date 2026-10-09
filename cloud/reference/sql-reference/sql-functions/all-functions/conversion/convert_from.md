---
description: Converts a binary string from the given data type to a Spice type.
---

# CONVERT\_FROM

### Syntax <a href="#syntax" id="syntax"></a>

#### CONVERT\_FROM(_binary\_value_ value\_to\_convert, _data\_type_ name\_of\_type) → varies <a href="#convert_frombinary_value-value_to_convert-data_type-name_of_type--varies" id="convert_frombinary_value-value_to_convert-data_type-name_of_type--varies"></a>

* binary\_value: The binary string to convert to a Spice data type.
* data\_type: The data type of the specified binary string.

**Examples**

{% code title="Returns a LIST from the specified JSON." %}
```sql
SELECT CONVERT_FROM('["apple", "strawberry", "banana"]', 'json')
-- ['apple', 'strawberry', 'banana']
```
{% endcode %}

{% code title="Returns a STRUCT from the specified JSON." %}
```sql
SELECT CONVERT_FROM('{"name":"Gnarly", "age":7, "car":null}', 'json')
-- {"name:"Gnarly","age":7}
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

You can convert from binary values that are in these data formats:

* BIGINT\_BE
* BIGINT\_HADOOPV
* BIGINT
* BOOLEAN\_BYTE
* DATE\_EPOCH\_BE
* DATE\_EPOCH
* DOUBLE\_BE
* DOUBLE
* FLOAT\_BE
* FLOAT
* INT\_BE
* INT\_HADOOPV
* INT
* JSON
* TIME\_EPOCH\_BE
* TIME\_EPOCH
* TIMESTAMP\_EPOCH\_BE
* TIMESTAMP\_EPOCH
* TIMESTAMP\_IMPALA\_LOCALTIMEZONE
* TIMESTAMP\_IMPALA
* UTF8
