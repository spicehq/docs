---
description: Decrypts a string produced by AES encryption.
---

# AES\_DECRYPT

### Syntax <a href="#syntax" id="syntax"></a>

#### AES\_DECRYPT(_ciphertext_ varchar, _key_ varchar) → varchar <a href="#aes_decryptciphertext-varchar-key-varchar--varchar" id="aes_decryptciphertext-varchar-key-varchar--varchar"></a>

* ciphertext: The string to be decrypted.
* key: The key to use to decrypt the ciphertext.

**Examples**

{% code title="AES_DECRYPT example" %}
```sql
SELECT AES_DECRYPT(UNBASE64('Fad52k2P1Y6rMZ0DBcg8SQ=='), 'mypassword')
-- Spice
```
{% endcode %}
