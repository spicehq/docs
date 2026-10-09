---
description: Converts a string that is encoded in the specified character set to UTF-8.
---

# TOASCII

### Syntax <a href="#syntax" id="syntax"></a>

#### TOASCII(_string\_expression_ STRING, _character\_encoding_ TYPE) → STRING <a href="#toasciistring_expression-string-character_encoding-type--string" id="toasciistring_expression-string-character_encoding-type--string"></a>

* string\_expression: The string to convert to the specified character encoding.
* character\_encoding: The character set that the string is encoded in.

**Examples**

{% code title="Converts the string" %}
```sql
SELECT TOASCII('Smith', 'Windows-1250')
-- Smith
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

These are the supported character encodings:

* ASCII
* Big5
* CP437
* CP737
* CP850
* CP852
* CP855
* CP857
* CP858
* CP860
* CP861
* CP862
* CP863
* CP865
* CP866
* CP869
* EUC-JP
* EUC-KR
* GBK
* ISCII
* ISO-2022-JP
* ISO-2022-KR
* ISO-8859-1
* ISO-8859-2
* ISO-8859-3
* ISO-8859-4
* ISO-8859-5
* ISO-8859-6
* ISO-8859-7
* ISO-8859-8
* ISO-8859-9
* ISO-8859-11
* ISO-8859-13
* ISO-8859-15
* KOI8-R
* KOI8-U
* UTF-8/UTF8
* UTF-16/UTF16
* UTF-32/UTF32
* Windows-1250
* Windows-1251
* Windows-1252
* Windows-1253
* Windows-1254
* Windows-1255
* Windows-1256
* Windows-1257
* Windows-1258
