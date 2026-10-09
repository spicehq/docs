---
description: Best practices and tips for getting the most out of Spice
---

# Best Practices

### Use `recent` tables

If you only need recent/near-real-time data, then use the `recent` tables. These tables include the last 30 mins of data and are very fast to query.

### Improve performance with ORDER BY, LIMIT, and OFFSET

You can significantly improve the performance of your SQL query by using limits and offsets on indexed columns. For example to fetch data in chunks:

```
SELECT *
FROM taxi_trips
WHERE id > 0
ORDER BY id
LIMIT 1000000
OFFSET 0
```

```
SELECT *
FROM taxi_trips
WHERE id > 0
ORDER BY id
LIMIT 1000000
OFFSET 1000000
```

### Use the Apache Arrow API and SDKs

The [Apache Arrow API](api/sql-query/apache-arrow-flight-api.md) uses Apache Arrow Flight to deliver results over a high-performance connection with no row limit.

SDKs like the [Python SDK](../sdks/python-sdk/) always use the Arrow API, so they are a convenient way to access Spice data.

### Combine SQL with Python or other languages

Because the Arrow API makes it convenient to fetch data into pandas and NumPy formats, it's easy to use popular data science libraries and tools. Instead of doing everything in SQL, leverage both SQL and your client programming language to get the job done.
