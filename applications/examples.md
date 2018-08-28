# Example Applications

Some example applications have been written to help with getting started using PNDA and knowing what to put in an application package.

## Spark Batch

The [spark-batch](https://github.com/pndaproject/example-applications/tree/master/spark-batch) example converts data from the master data set into parquet format.

## Spark Streaming - Kafka to HBase

The [spark-streaming](https://github.com/pndaproject/example-applications/tree/master/spark-streaming) example writes data from Kafka into an HBase table.

The [Spark Streaming and HBase tutorial](ksh.md) provides an in-depth look at the app.

## Spark Streaming - Kafka to OpenTSDB

The [kafka-spark-opentsdb](https://github.com/pndaproject/example-applications/tree/master/kafka-spark-opentsdb) example writes data from Kafka in [OpenTSDB](../timeseries/opentsdb.md) metric time series.

The [Spark Streaming and OpenTSDB tutorial](kso.md) provides an in-depth look at the app.

## Flink Streaming

The [flink-streaming](https://github.com/pndaproject/example-applications/tree/develop/flink-streaming-word-count) example reads events from a network socket and performs stream processing.
