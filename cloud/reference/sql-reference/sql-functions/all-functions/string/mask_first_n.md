---
description: >-
  Returns a masked version of a string with the first num_chars characters
  masked. By default, if you do not provide a mask value, the first four
  characters are masked.
---

# MASK\_FIRST\_N

### Syntax <a href="#syntax" id="syntax"></a>

#### MASK\_FIRST\_N(_expression_ varchar, _num\_chars_ int, _uc\_mask_ varchar, _lc\_mask_ varchar, _num\_mask_ varchar) → varchar <a href="#mask_first_nexpression-varchar-num_chars-int-uc_mask-varchar-lc_mask-varchar-num_mask-varchar--varch" id="mask_first_nexpression-varchar-num_chars-int-uc_mask-varchar-lc_mask-varchar-num_mask-varchar--varch"></a>

* expression: The string to mask.
* num\_chars: The number of characters to mask.
* uc\_mask: Controls the mask character for upper case letters.
* lc\_mask: Controls the mask character for lower case letters.
* num\_mask: Controls the mask character for numbers.

**Examples**

{% code title="MASK_FIRST_N example" %}
```sql
SELECT MASK_FIRST_N('abcd-ABCD-1234')
-- xxxx-ABCD-1234
```
{% endcode %}

{% code title="MASK_FIRST_N example containing optional mask value." %}
```sql
SELECT MASK_FIRST_N('abcd-ABCD-1234', 2)
-- xxcd-ABCD-1234
```
{% endcode %}

{% code title="MASK_FIRST_N example containing optional arguments." %}
```sql
SELECT MASK_FIRST_N('Aa12-ABCD-1234', 4, 'U', 'u', '#')
-- Uu##-ABCD-1234
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

By default, upper case letters are converted to `X`, lower case letters are converted to `x`, and numbers are converted to `n`. You can override the characters used in the mask by supplying optional arguments. The second argument controls the mask character for upper case letters, the third argument for lower case letters, and the fourth argument for numbers.

\
