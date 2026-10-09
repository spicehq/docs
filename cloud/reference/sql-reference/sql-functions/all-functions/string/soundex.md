---
description: Returns a string that contains a phonetic representation of the input string.
---

# SOUNDEX

### Syntax <a href="#syntax" id="syntax"></a>

#### SOUNDEX(_expression_ varchar) → varchar <a href="#soundexexpression-varchar--varchar" id="soundexexpression-varchar--varchar"></a>

* expression: The expression for which a representation of the pronunciation is returned.

**Examples**

{% code title="SOUNDEX example" %}
```sql
SELECT SOUNDEX('Smith'), SOUNDEX('Smythe')
-- EXPR$0, EXPR$1
-- S530, S530
```
{% endcode %}

{% code title="SOUNDEX example" %}
```sql
SELECT SOUNDEX('the data lake'), SOUNDEX('the data lakehouse'), SOUNDEX('the data warehouse')
-- EXPR$0, EXPR$1, EXPR$2
-- T334, T334, T336
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

You can use this function to determine whether two strings (for example, similar last names ‘Smith’ and ‘Smythe’) have similar pronounciations in the English language. This function uses the [Soundex phonetic algorithm](https://en.wikipedia.org/wiki/Soundex), which is described in [Soundex System](https://www.archives.gov/research/census/soundex). This function returns only four characters (one letter and three digits). As a result, the output is primarily determined by the first few syllables of the input, rather than the entire string.

\
