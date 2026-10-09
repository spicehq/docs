---
description: Snowflake Data Connector Documentation
---

# Snowflake Data Connector

The Snowflake Data Connector enables federated SQL queries across datasets in the [Snowflake Cloud Data Warehouse](https://www.snowflake.com/).

```yaml
datasets:
  - from: snowflake:DATABASE.SCHEMA.TABLE
    name: table
    params:
      snowflake_warehouse: COMPUTE_WH
      snowflake_role: accountadmin
```

{% hint style="info" %}
Unquoted identifiers are normalized to lowercase by Spice. Snowflake normalizes unquoted identifiers to uppercase, so unquoted identifiers in the `from` field should be UPPERCASED (e.g. `snowflake:MY_DATABASE.MY_SCHEMA.MY_TABLE`). To reference a table created with mixed-case in Snowflake, wrap it in double quotes: `snowflake:MY_DATABASE.MY_SCHEMA."mixedCaseTable"`. See [Snowflake identifier resolution](https://docs.snowflake.com/en/sql-reference/identifiers-syntax#label-identifier-casing) and [Identifier Case Sensitivity](README.md#identifier-case-sensitivity-and-quoting).
{% endhint %}

## Configuration

### `from`

A Snowflake fully qualified table name (database.schema.table). For instance `snowflake:SNOWFLAKE_SAMPLE_DATA.TPCH_SF1.LINEITEM` or `snowflake:TAXI_DATA."2024".TAXI_TRIPS`

### `name`

The dataset name. This will be used as the table name within Spice.

### `params`

| Parameter Name                     | Description                                                                                                     |
| ---------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| `snowflake_warehouse`              | Optional, specifies the [Snowflake Warehouse](https://docs.snowflake.com/en/user-guide/warehouses-tasks) to use |
| `snowflake_role`                   | Optional, specifies the role to use for accessing Snowflake data                                                |
| `snowflake_account`                | Required, specifies the Snowflake account-identifier                                                            |
| `snowflake_username`               | Required, specifies the Snowflake username to use for accessing Snowflake data                                  |
| `snowflake_password`               | Optional, specifies the Snowflake password to use for accessing Snowflake data                                  |
| `snowflake_private_key_path`       | Optional, specifies the path to Snowflake private key                                                           |
| `snowflake_private_key_passphrase` | Optional, specifies the Snowflake private key passphrase                                                        |

## Auth

The connector supports password-based and [key-pair](https://docs.snowflake.com/en/user-guide/key-pair-auth) authentication. Login requires the account identifier ('orgname-accountname' format) - use [Finding the organization and account name for an account](https://docs.snowflake.com/en/user-guide/admin-account-identifier#finding-the-organization-and-account-name-for-an-account) instructions.

## Example

```yaml
datasets:
  - from: snowflake:SNOWFLAKE_SAMPLE_DATA.TPCH_SF1.LINEITEM
    name: lineitem
    params:
      snowflake_warehouse: COMPUTE_WH
      snowflake_role: accountadmin
```

{% hint style="warning" %}
**Limitations**

1. Account identifier does not support the [Legacy account locator in a region format](https://docs.snowflake.com/en/user-guide/admin-account-identifier#format-2-legacy-account-locator-in-a-region). Use [Snowflake preferred name in organization format](https://docs.snowflake.com/en/user-guide/admin-account-identifier#format-1-preferred-account-name-in-your-organization).
2. The connector supports password-based and [key-pair](https://docs.snowflake.com/en/user-guide/key-pair-auth) authentication.
{% endhint %}
