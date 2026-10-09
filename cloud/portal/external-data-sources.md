---
description: Connect external data sources to Spice.ai
hidden: true
---

# External Data Sources

Access and query external data sources in addition to community datasets including the ability to execute SQL joins across both by [creating custom datasets](datasets-and-views.md).

External Data Sources initially supports connecting to [PostgreSQL](https://www.postgresql.org/) and [MySQL](https://www.mysql.com/) with more data sources coming soon.

### Adding an External Data Source

External Data Sources are added and managed through [organizations](organizations.md) and are available to all Spice projects within the organization. They are private and are not visible or accessible to projects in other organizations.

Navigate to the organization's **Settings** and then the **Data Sources** section.

Click **Add Data Source** to show the **New Data Source** dialog.

Select the data source and then complete the required connection details.

Once the data source is connected the data source will be made available through its **name**, which will be the schema name for SQL queries. E.g. naming the data source connection "**mydb**" will enable selecting tables with the SQL ` SELECT * FROM`` `` `**`mydb`**`.{table}.`, click the vertical dots to the right of the connection to edit or delete it.

To edit or delete the data source, click the vertical ellipses menu.

### TLS and certificate verification

Connections to PostgreSQL and MySQL data sources are encrypted with TLS by default. When the connection details name an SSL mode, that mode is used. When they do not, Spice.ai resolves one instead of falling back to a mode that permits an unencrypted connection.

**PostgreSQL**

| Connection string           | Resolved `sslmode`                                                    |
| --------------------------- | --------------------------------------------------------------------- |
| No `sslmode`                | `verify-full` — the certificate chain and the hostname are both checked |
| No `sslmode`, Amazon RDS or Aurora host | `require` — TLS is required, the certificate is not verified |
| Any explicit `sslmode`      | Used verbatim, including `disable`                                     |

Amazon RDS and Aurora endpoints resolve to `require` rather than `verify-full` because their certificates are issued by Amazon's private per-region certificate authorities, which the system trust store does not carry. Encryption stays mandatory for these hosts.

Amazon's RDS root certificate authorities *are* trusted when Spice.ai tests a connection, so testing an RDS or Aurora source that asks for `sslmode=require` or stronger succeeds without any additional certificate configuration.

{% hint style="warning" %}
Configuring a PostgreSQL source for [database replication](../../features/database-replication-and-cdc.md) requires TLS with a verified certificate. A connection string is refused when it turns TLS off (`sslmode=disable`, `ssl=0`), accepts whatever certificate the server presents (`sslmode=no-verify`), or checks the certificate chain without checking that the certificate was issued for the host (`uselibpqcompat=true` with `require` or `verify-ca`). Use `sslmode=verify-full`.
{% endhint %}

Testing a saved connection honors the mode the connection stores rather than refusing it, so a data source that names a weaker mode explicitly keeps that mode.

**MySQL**

| Connection string                                                          | Resolved `mysql_sslmode` |
| -------------------------------------------------------------------------- | ------------------------ |
| No SSL parameter                                                           | `required`               |
| No SSL parameter, Amazon RDS or Aurora host                                | `preferred`              |
| `sslmode=disabled`, `tls=false`                                            | `disabled`               |
| `sslmode=preferred`, `tls=skip-verify`, `sslaccept=accept_invalid_certs`    | `preferred`              |
