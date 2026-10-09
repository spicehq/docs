---
description: >-
  Returns the input string with the first letter of each word in uppercase and
  the subsequent letters in the word are in lowercase).
---

# INITCAP

### Syntax <a href="#syntax" id="syntax"></a>

#### INITCAP(_expression_ varchar) → varchar <a href="#initcapexpression-varchar--varchar" id="initcapexpression-varchar--varchar"></a>

* expression: Input string.

**Examples**

{% code title="INITCAP example" %}
```sql
SELECT INITCAP('a guide to data lakehouses')
-- A Guide To Data Lakehouses
```
{% endcode %}

{% code title="INITCAP example" %}
```sql
SELECT INITCAP('a guide to data lakeHouses')
-- A Guide To Data Lakehouses
```
{% endcode %}
