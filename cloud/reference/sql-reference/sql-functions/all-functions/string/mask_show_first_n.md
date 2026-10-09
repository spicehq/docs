---
description: >-
  Returns a masked version of a string with the first num_chars characters
  unmasked. By default, if you do not provide a value, the first four characters
  are shown.
---

# MASK\_SHOW\_FIRST\_N

### Syntax <a href="#syntax" id="syntax"></a>

#### MASK\_SHOW\_FIRST\_N(_expression_ varchar, _num\_chars_ int, _uc\_mask_ varchar, _lc\_mask_ varchar, _num\_mask_ varchar) → varchar <a href="#mask_show_first_nexpression-varchar-num_chars-int-uc_mask-varchar-lc_mask-varchar-num_mask-varchar" id="mask_show_first_nexpression-varchar-num_chars-int-uc_mask-varchar-lc_mask-varchar-num_mask-varchar"></a>

* expression: The string to mask.
* num\_chars: The number of characters to unmask.
* uc\_mask: Controls the mask character for upper case letters.
* lc\_mask: Controls the mask character for lower case letters.
* num\_mask: Controls the mask character for numbers.

**Examples**

{% code title="MASK_SHOW_FIRST_N example" %}
```sql
SELECT MASK_SHOW_FIRST_N('abcd-ABab-1234')
-- abcd-XXxx-nnnn
```
{% endcode %}

{% code title="MASK_SHOW_FIRST_N example containing optional value." %}
```sql
SELECT MASK_SHOW_FIRST_N('abcd-ABab-1234', 2)
-- abxx-XXxx-nnnn
```
{% endcode %}

{% code title="MASK_SHOW_FIRST_N example containing optional arguments." %}
```sql
SELECT MASK_SHOW_FIRST_N('Aa12-ABab-1234', 4, 'U', 'u', '#')
-- Aa12-UUuu-####
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

By default, upper case letters are converted to `X`, lower case letters are converted to `x`, and numbers are converted to `n`. You can override the characters used in the mask by supplying optional arguments. The second argument controls the mask character for upper case letters, the third argument for lower case letters, and the fourth argument for numbers.

\
