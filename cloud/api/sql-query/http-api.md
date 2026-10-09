---
description: Query with SQL via the HTTP API
---

# HTTP API

Data may be queried by posting SQL to the `/v1/sql` API and `/v1/firesql` API for Firecached data. For documentation on the Spice Firecache see [Broken link](/broken/pages/q4t8COjPZrT3eUwQXMBw "mention").

See [Tables](/broken/pages/raer32Dk4PPuKbzd1GLs) for a list of tables to query or browse the example queries listed in the menu.

#### Requirements and limitations

* An API key is required for all SQL queries.
* Results are limited to 500 rows. Use the [Apache Arrow Flight API](apache-arrow-flight-api.md) to fetch up to 1M rows in a single query or the [Async HTTP API](/broken/pages/INalDm4gLljuBB16j2bY) to fetch results with paging.
* Requests are limited to 90 seconds.

{% openapi-operation spec="spiceai-platform-api" path="/v1/sql" method="post" %}
[OpenAPI spiceai-platform-api](https://4401d86825a13bf607936cc3a9f3897a.r2.cloudflarestorage.com/gitbook-x-prod-openapi/raw/ad44c61950b5a12bd7e25a256a4e88ee4fadb61c6b68c2e35bd5188449f7939e.yaml?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=dce48141f43c0191a2ad043a6888781c%2F20260105%2Fauto%2Fs3%2Faws4_request&X-Amz-Date=20260105T221840Z&X-Amz-Expires=172800&X-Amz-Signature=6915a054049f57f3a547c3954751146a7e20dc9e352875222c5af19791f70bcf&X-Amz-SignedHeaders=host&x-amz-checksum-mode=ENABLED&x-id=GetObject)
{% endopenapi-operation %}
