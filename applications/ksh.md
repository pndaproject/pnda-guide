# Spark Streaming and HBase tutorial

## Objectives

In this lab you will discover how to compile and deploy a [Spark Streaming](../repos/example-applications/spark-streaming/README.md) application and then use [Impala](../query/impala.md) to query the data it writes to [HBase](https://hbase.apache.org/).

Applications that run on PNDA are packaged as `tar.gz` archives and pushed to an application repository. The `tar` archive contains all the binaries and configuration required to run the application. 

You'll learn how to use the [Deployment Manager API](../repos/platform-deployment-manager/README.md) to deploy a  package from the package repository and run it on the cluster.

# Prerequisites

Make sure you are familiar with the following before proceeding with the lab:

- The [getting started](../gettingstarted/README.md) guide will introduce you to many concepts used in this lab.
- The [console](../console/README.md) is your starting point for interacting with a PNDA cluster. All of the main features of PNDA can be accessed from the console.
- The [Deployment Manager API](../repos/platform-deployment-manager/README.md) provides a mechanism for discovering available packages and deploying & undeploying functionality. It works with a well-defined package structure to make it fast and straightforward to get real scalable analytics running on the platform.
- Make sure that your package repository is correctly configured as described in the [platform requirements](../provisioning/platform_requirements.md).
- The [platform-package-repository](../repos/platform-package-repository/README.md) tool lets you upload packages to OpenStack Swift. 
- - Read the technical notes in the [example repository](../repos/example-applications/spark-streaming/README.md).

## Requirements

Make sure you have the following installed on your development computer: 

- a REST client such as [Poster](https://addons.mozilla.org/en-US/firefox/addon/poster/)
- [Maven](https://maven.apache.org/docs/3.0.5/release-notes.html) 3.0.5 or later
- [Java 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

## Running the application

### Download the application code base

The PNDA distribution is available on GitHub at:

 * [https://github.com/pndaproject](https://github.com/pndaproject)

Clone the [spark-streaming](../repos/example-applications/spark-streaming/README.md) repository. 

### Compile with maven

```
mvn clean package
```

### Upload the app package to the PNDA app repository

Use the [platform-package-repository](../repos/platform-package-repository/README.md) tool to upload the application tar.gz file to your application repository.

### Run the application

Use the graphical interface in the console to deploy [packages](../console/packages.md) and start [applications](../console/applications.md).  


## Producing test data

The technical notes in the [example repository](../repos/example-applications/spark-streaming/README.md) describe how to set up a test producer that will create suitable test data for consumption by this example application.

## Understanding what the application is doing

The messages on Kafka are [Avro](https://avro.apache.org/docs/current/) encoded and look like this:
``` 
 src: "test-src"
 timestamp: 1453480869657
 host_ip: "0.0.0.0"
 rawdata: "a=1;b=2;c=?;gen_ts=?
```
 - a and b are fixed
 - c is a varying integer value
 - gen_ts is the same as timestamp

The Spark Streaming app copies each message into an HBase row:
```
rowkey - shard_gen_ts_c
cf:a
cf:b
cf:c
cf:gen_ts
```
and inserts an extra column with the time at which the message was processed:
```
cf:proc_ts
```

If you were to calculate `proc_ts - gen_ts` you would learn how long each message sat in the Kafka queue before being processed.

The shard part of the `rowkey` cycles between 0 and the setting `hbase_shards` used in the PUT body. This ensures that the writes to HBase are evenly distributed around the multiple region servers that comprise HBase.

The Spark Streaming code is located in:
```
streaming-app/src/main/scala/com/cisco/pnda/examples/spark/KafkaToHbaseApp.scala **TODO** check path
```

createPipeline sets up the processing graph with three main parts:
- `readFromKafka` - calls `KafkaUtils.createDirectStream` to read from kafka
- `parseMessages` - calls `DStream.flatMap` to apply parsing code to decode each message as read from kafka
- `writeToHbase` - calls `DStream.mapPartitions` to write the parsed messages into HBase

You can learn more about these Spark Streaming API calls and the rest of the API by reading the [Spark Streaming documentation](http://spark.apache.org/docs/1.3.0/streaming-programming-guide.html).

Then the number of messages written in each partition are summed with a reduce call and the total number of messages written to HBase in a given batch is printed to the driver output:
```
writeCounts.reduce(_ + _).print(1);
```

You can see these log messages at "http://logstash-ip:logstash-port" with a query "logstash-query".

## Checking the output

### Compile the Client

Edit the application.properties in src/main/resources with the impala endpoint IP address and port number, then compile it with maven:

```
cd impala-jdbc-client
mvn clean package
```

### Query the data
The supplied sample application executes the following SQL query to compute the average value of cf:c between two timestamps x and y:
```
select round(avg(cast(col as int)),2) as average from luke-skywalker-example_table where id between x and y
```
When you run the client you will see the query printed to the console, try to work out why the where clause is more complex in reality. Hint: it is to do with the shard part of the rowkey.

Metadata is defined in the hive metastore which maps the HBase columns onto SQL fields. This metadata is provisioned as part of the application package in the hbase.json file.

Only the SQL field that corresponds to the HBase rowkey can be considered *indexed*, and it is generally good practice to limit the quantity of data being considered with a where clause on that field.

```
java -cp target/impala-jdbc-client-1.0.jar:driver/Cloudera_ImpalaJDBC4_2.5.5.1007/* com.cisco.pnda.examples.impalaclient.Client example_table 1453480869657 1453480869942
```

## Cleaning up

If you no longer need it, you can stop the application and undeploy the package using the console.
