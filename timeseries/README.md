# Time Series

## [OpenTSDB](opentsdb.md)

OpenTSDB is a scalable time series database that lets you store and serve massive amounts of time series data, without losing granularity. In PNDA, a custom application (reading data from Kafka or HDFS for example) could write time series and store them in OpenTSDB.

## [Grafana](grafana.md)

Grafana is a graph and dashboard builder for visualizing time series metrics. It is pre-configured to connect to OpenTSDB as its data source. It is much easier to create dashboards in Grafana than using the OpenTSDB user interface.