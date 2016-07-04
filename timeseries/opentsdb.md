# OpenTSDB #

[OpenTSDB](http://opentsdb.net) is a scalable time series database that can store and serve massive amounts of time series data without losing granularity. 

OpenTSDB consists of a Time Series Daemon (TSD) as well as set of command line utilities. Interaction with OpenTSDB is primarily achieved by running one or more of the TSDs. Each TSD is independent. There is no master, no shared state so you can run as many TSDs as required to handle any load you throw at it. Each TSD uses the open source database HBase to store and retrieve time-series data. The HBase schema is highly optimized for fast aggregations of similar time series to minimize storage space. 

## Installation ##

When deploying PNDA, there is an option to set up OpenTSDB. Once the cluster is deployed, OpenTSDB is ready to be used. The only thing left to do is to create the time series.

## Time series creation ##

On the server running OpenTSDB, create the `metric sys.cpu.user` with this command:

    sudo /usr/share/opentsdb/bin/tsdb/tsdb mkmetric sys.cpu.user --config /etc/opentsdb/opentsdb.conf

## Writing ##

A time series is a collection of data points for an identity over time.

In OpenTSDB, a time series data point consists of:

- A metric name.
- A UNIX timestamp (seconds or milliseconds since Epoch).
- A value (64 bit integer or single-precision floating point value).
- A set of tags (key-value pairs) that describe the time series the point belongs to.

Take the following data point as an example:

    sys.cpu.user 1234567890 42 host=123 type=router cpu=0

In this data point,

  - the metric name is `sys.cpu.user`
  - the timestamp is 1234567890
  - the value is 42
  - tags are `host`, `type` and `cpu`

It is important to think about time series naming optimization in order to determine when aggregation is useful.
Think about cardinality: let's say you have another tag type such as firewall. If you are interested in
aggregated value by type, it will be more efficient to move the type from the tag section to the metric name (`router.sys.cpu.user` and `firewall.sys.cpu.user`).

The most basic operations for writting a data point are:

- telnet and put `router.sys.cpu.user 1234567890 42 host=123 type=router cpu=0`
- HTTP POST with JSON to `http://{host}:{port}/api/put`

**Ingesting PNDA input data**

One technique is to develop a Spark Streaming application that reads from Kafka and writes the processing results to OpenTSDB in the appropriate time series based on the app logic. We provide sample code for a Spark streaming skeleton.

## Reading ##

OpenTSDB offers a number of means to extract data, such as CLI tools, an HTTP API and as a GnuPlot graph. Querying with OpenTSDB's tag based system can be a bit tricky, so read through this page, and see the [OpenTSDB HTTP API format](http://opentsdb.net/docs/build/html/user_guide/query/index.html) document for further information.

Here is a snippet of the key elements for performing a query:

| endpoint | name |	required |	type | description | default | example |
|:-----------------|:--------:|:---------:|:-------|:---------------------------|:-----------------|----------------|
|/api/query (GET)|start|yes|string,integer|The start time for the query.<br>This can be a relative or absolute timestamp. | n/a |1h-ago<br>2015/10/15-11:21:02<br>1444859743020 |
|-|end|no|string,integer|An end time for the query. If not supplied, the TSD will assume the local system time on the server.<br>This may be a relative or absolute timestamp.| n/a |10s-ago<br>2015/10/15-11:21:02<br> 1444859743020 |
|-|ms|no|boolean|Output data timestamp in ms or s| false |false<br>true|
|-|m|yes|array of sub queries|Requires at least one sub query, a means of selecting which time series should be included in the result set| n/a |See sub query section|
|/api/query/last (GET)|timeseries|yes|string|<metric_name>[{<tag_name1>=<tag_value1>[,...<tag_nameN>=<tag_valueN>]}]| n/a |test{host=router01} |
|-|back_scan|no|integer|Number of hours to search in the past| n/a |24 |
|/api/suggest (GET)|type|yes|string|The type of data to auto complete on| n/a |metric<br>tagk<br>tagv |
|-|q|no|string|A string to match on for the given type| n/a |sys.cpu |
|-|max|no|integer|Max. number of suggested results to return| 25 |10|

Here is a snippet of the key elements for the sub metric query syntax (for /api/query m param):

| | | | | | |
|:--------|:---------:|:-------|:---------------------------|:-----------------:|:-------|
|\<aggregator\>|yes|string|The name of an aggregation function to use.| n/a |sum<br>avg<br>dev<br>min<br>max|
|\<downsample\>|no|string|An optional downsampling function to reduce the amount of data returned.| n/a |5m-avg|
|\<metric\>|yes|string|The name of a metric stored in the system.| n/a |sys.cpu.user|
|\<tags\>|no|Map|To drill down to specific timeseries or group results by tag, supply one or more map values in the same format as the query string.  Note that if no tags are specified, all metrics in the system will be aggregated into the results.| n/a |{host=router01}|
|\<filters\>|no|List|Filters the time series emitted in the results. Note that if no filters are specified, all time series for the given metric will be aggregated into the results. The type precedes the filter expression in parentheses.<br>Whether or not results are grouped depends on which curly bracket the filter is in. Two curly braces are now supported per metric query. The first set is the group by filter and the second is a non group by filter, e.g. `{host=web01}{colo=regexp(sjc.\*)}`. If you only want to filter without grouping then the first curly set must be empty, e.g. `{}{host=router\*}`| n/a |m=avg:sys.cpu.percent.idle{host=router01,plugin_instance=0|1}|
