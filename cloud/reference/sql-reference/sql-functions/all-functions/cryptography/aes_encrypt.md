---
description: Encrypts a string using AES encryption.
---

# AES\_ENCRYPT

### Syntax <a href="#syntax" id="syntax"></a>

#### AES\_ENCRYPT(_plaintext_ varchar, _key_ varchar) → varchar <a href="#aes_encryptplaintext-varchar-key-varchar--varchar" id="aes_encryptplaintext-varchar-key-varchar--varchar"></a>

* plaintext: The string to be encrypted.
* key: The key to use to encrypt the plaintext.

**Examples**

{% code title="AES_ENCRYPT example" %}
```sql
SELECT BASE64(AES_ENCRYPT('Spice', 'mypassword'))
-- Fad52k2P1Y6rMZ0DBcg8SQ==
```
{% endcode %}
