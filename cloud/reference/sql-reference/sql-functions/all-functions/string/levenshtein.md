---
description: Computes the Levenshtein distance between two input expressions.
---

# LEVENSHTEIN

### Syntax <a href="#syntax" id="syntax"></a>

#### LEVENSHTEIN(_expression1_ varchar, _expression2_ varchar) → int <a href="#levenshteinexpression1-varchar-expression2-varchar--int" id="levenshteinexpression1-varchar-expression2-varchar--int"></a>

* expression1: The first string expression.
* expression2: The second string expression.

**Examples**

{% code title="LEVENSHTEIN example" %}
```sql
SELECT LEVENSHTEIN('spice', 'iceberg')
-- 6
```
{% endcode %}
