---
description: Each call returns a random generated number between 0 and 1 for each row.
---

# RANDOM

### Syntax <a href="#syntax" id="syntax"></a>

#### RANDOM() → double <a href="#random--double" id="random--double"></a>

**Examples**

{% code title="RANDOM example" %}
```sql
SELECT RANDOM()
-- 0.24943567857336457
```
{% endcode %}

#### RANDOM(_seed_ int32) → double <a href="#randomseed-int32--double" id="randomseed-int32--double"></a>

* seed: Seed value.

**Examples**

{% code title="RANDOM example" %}
```sql
SELECT RANDOM(4000)
-- 0.18633151868393985
```
{% endcode %}

### Usage Notes <a href="#usage-notes" id="usage-notes"></a>

If a seed value is specified, `RANDOM` generates a unique number for each row. If the same seed is used to call `RANDOM` on the same row multiple times, the same generated number will be returned each time.
