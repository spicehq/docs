---
description: Returns a masked version of a string.
---

# MASK

### Syntax <a href="#syntax" id="syntax"></a>

#### MASK(_expression_ varchar, _uc\_mask_ varchar, _lc\_mask_ varchar, _num\_mask_ varchar) → varchar <a href="#maskexpression-varchar-uc_mask-varchar-lc_mask-varchar-num_mask-varchar--varchar" id="maskexpression-varchar-uc_mask-varchar-lc_mask-varchar-num_mask-varchar--varchar"></a>

* expression: The string to mask.
* uc\_mask: Controls the mask character for upper case letters.
* lc\_mask: Controls the mask character for lower case letters.
* num\_mask: Controls the mask character for numbers.

**Examples**

{% code title="MASK example" %}
```sql
SELECT MASK('abcd-ABCD-1234')
-- xxxx-XXXX-nnnn
```
{% endcode %}

{% code title="MASK example containing optional arguments." %}
```sql
SELECT MASK('abcd-ABCD-1234', 'U', 'u', '#')
-- uuuu-UUUU-####
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

By default, upper case letters are converted to `X`, lower case letters are converted to `x`, and numbers are converted to `n`. You can override the characters used in the mask by supplying optional arguments. The first argument controls the mask character for upper case letters, the second argument for lower case letters, and the third argument for numbers.

\
