---
icon: plug
---

# Grafana

Create custom dashboards and visualizations using the FlightSQL or Infinity Grafana Plugins:

* [FlightSQL Grafana Plugin](https://grafana.com/grafana/plugins/influxdata-flightsql-datasource/)
* [Infinity Grafana Plugin](https://grafana.com/grafana/plugins/yesoreyeram-infinity-datasource/)

## Setup with FlightSQL Grafana Plugin

### Installing FlightSQL Grafana Plugin

Use the [FlightSQL Grafana Plugin installation instructions](https://grafana.com/grafana/plugins/influxdata-flightsql-datasource/?tab=installation) for an installation reference.

In the Grafana installation, navigate to `Administration` -> `Plugins and data` -> `Plugins`. Select "State: All" and search for "FlightSQL" in the Search box. Select "FlightSQL" in the list of plugins.

Click the "Install" button to install the plugin. Once installed, a new data source can be added.

### Adding Spice as a data source

Click the "Add new data source" button from the FlightSQL plugin menu.

In the data source menu, provide the following options:

* `Host:Port` - set to `flight.spiceai.io:443` to connect to the Spice.ai Cloud Platform
* `Auth Type` - set to `token`
* `Token` - set to your [Spice.ai API key](../portal/apps/api-keys.md).

Once these options are set, click "Save & Test". Grafana should report "OK" if the configuration is correct.

### Creating visualizations

Create a visualization using the FlightSQL Spice.AI data source. Select the configured datasource from the list of datasources in the visualization creator.

Create your query using the SQL builder, or switch to a plain SQL editor by clicking the "Edit SQL" button. In this example, the query retrieves the latest query execution times from the configured project instance and displays them as a line graph.

Grafana may not automatically update the visualization when changes are made to the query. To force the visualization to update with new query changes, click the "Query Inspector" button, and click "Refresh".
