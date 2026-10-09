---
description: >-
  Concatenates a group of rows into a list of strings and places a separator
  between them.
---

# LISTAGG

### Syntax <a href="#syntax" id="syntax"></a>

#### LISTAGG ( \[ALL | DISTINCT] measure\_expr \[, ‘delimiter’] ) \[WITHIN GROUP ( ORDER BY measure\_expr \[ASC | DESC] )] <a href="#listagg-all-distinct-measure_expr-delimiter-within-group-order-by-measure_expr-asc-desc" id="listagg-all-distinct-measure_expr-delimiter-within-group-order-by-measure_expr-asc-desc"></a>

* ALL: Keeps duplicate values in the return list. This is the default behavior.
* DISTINCT: Removes duplicate values from the return list.
* measure\_expr: A string column or value.
* delimiter: Designates a string literal to separate the measure column values. If a delimiter is not specified, will default to `NULL`.
* \[WITHIN GROUP ( ORDER BY measure\_expr \[ASC | DESC] )]: Determines the order in which the concatenated values are returned. Using one of the optional keywords - `ASC` or `DESC` - returns the values in ascending or descending order, respectively. The default order is ascending.

**Examples**

{% code title="LISTAGG example" %}
```sql
SELECT LISTAGG(city, '; ')
FROM taxi_trips

-- AGAWAM; CUSHMAN; BARRE; BELCHERTOWN; BLANDFORD; BRIMFIELD; CHESTER; CHESTERFIELD; CHICOPEE; CHICOPEE
```
{% endcode %}

{% code title="LISTAGG example" %}
```sql
SELECT LISTAGG(city, ', ') 
WITHIN GROUP (ORDER BY city DESC) "city_list" 
FROM taxi_trips

-- city_list
-- ZWOLLE; ZWINGLE; ZURICH; ZUNI; ZUNI; ZUMBROTA; ZORTMAN; ZOLFO SPRINGS; ZOE; ZOARVILLE; ZIRCONIA; ZIO
```
{% endcode %}

{% code title="LISTAGG example" %}
```sql
SELECT state,
LISTAGG(DISTINCT city, '| ')
WITHIN GROUP (ORDER BY city) "city_list"
FROM taxi_trips
GROUP BY state
ORDER BY state DESC

-- state, city_list
-- WY, 82057| ACME| AFTON| ALADDIN| ALBIN| ALCOVA| ARAPAHOE| ARMINTO| ARVADA| AUBURN| BAGGS| BAIROIL| BANNE
-- WV, 24939| 25242| 25536| ABRAHAM| ADRIAN| ADVENT| ALBRIGHT| ALEXANDER| ALGOMA| ALKOL| ALUM BRIDGE| ALUM
-- WI, ABBOTSFORD| ABRAMS| ADAMS| ADELL| ALBANY| ALGOMA| ALLENTON| ALLOUEZ| ALMA| ALMA CENTER| ALMENA| ALMO
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

If the return list is greater than 32768 bytes, then Spice truncates it.
