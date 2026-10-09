# SQL Reference

The Spice Cloud Platform hosts managed Spice.ai Open Source runtime instances.

See the [Spice.ai Open Source SQL Reference](https://spiceai.org/docs/reference/sql) for a complete guide of supported SQL syntax (PostgreSQL-dialect), data types, functions, and commands.

## Identifier Case Sensitivity

Spice follows PostgreSQL conventions for identifier handling: unquoted identifiers (table and column names) are normalized to **lowercase**. To reference a table or column with uppercase or mixed-case characters, wrap the identifier in double quotes.

```sql
-- These are equivalent (both reference the lowercase table name)
SELECT * FROM my_table;
SELECT * FROM MY_TABLE;

-- Double quotes preserve case
SELECT * FROM "MyTable";
SELECT * FROM "MyTable"."MixedCaseColumn";
```

This behavior also applies to [dataset names](/broken/pages/TqUYlnf0jpMU6k3dv9vL#identifier-case-sensitivity-and-quoting) configured in the `from` and `name` fields of the spicepod.
