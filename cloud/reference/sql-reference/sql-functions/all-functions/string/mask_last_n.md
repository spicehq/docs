---
description: >-
  Returns a masked version of a string with the last num_chars characters
  masked. By default, if you do not provide a mask value, the last four
  characters are masked.
---

# MASK\_LAST\_N

### Syntax <a href="#syntax" id="syntax"></a>

#### MASK\_LAST\_N(_expression_ varchar, _num\_chars_ int, _uc\_mask_ varchar, _lc\_mask_ varchar, _num\_mask_ varchar) → varchar <a href="#mask_last_nexpression-varchar-num_chars-int-uc_mask-varchar-lc_mask-varchar-num_mask-varchar--varcha" id="mask_last_nexpression-varchar-num_chars-int-uc_mask-varchar-lc_mask-varchar-num_mask-varchar--varcha"></a>

* expression: The string to mask.
* num\_chars: The number of characters to mask.
* uc\_mask: Controls the mask character for upper case letters.
* lc\_mask: Controls the mask character for lower case letters.
* num\_mask: Controls the mask character for numbers.

**Examples**

{% code title="MASK_LAST_N example" %}
```sql
SELECT MASK_LAST_N('abcd-ABCD-1234')
-- abcd-ABCD-nnnn
```
{% endcode %}

{% code title="MASK_LAST_N example containing optional mask value." %}
```sql
SELECT MASK_LAST_N('abcd-ABCD-1234', 2)
-- abcd-ABCD-12nn
```
{% endcode %}

{% code title="MASK_LAST_N example containing optional arguments." %}
```sql
SELECT MASK_LAST_N('abcd-ABCD-Aa12', 4, 'U', 'u', '#')
-- abcd-ABCD-Uu##
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

By default, upper case letters are converted to `X`, lower case letters are converted to `x`, and numbers are converted to `n`. You can override the characters used in the mask by supplying optional arguments. The second argument controls the mask character for upper case letters, the third argument for lower case letters, and the fourth argument for numbers.

\
