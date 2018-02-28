# Versions
The following table lists the technologies used in this PNDA release.

Some technologies depend on which distribution of Hadoop is chosen, as PNDA can be deployed using either Cloudera CDH or Hortonworks HDP.

## Common to all variants of PNDA

| Technology | Version |
|---|---|
|SaltStack|2015.8.11|
|OpenStack Heat templates|2014-10-16|
|AWS CFN templates|2010-09-09|
|Kafka|0.11.0.0|
|Zookeeper for Kafka|3.4.11|
|JMX Proxy|3.2.0|
|Kafka Manager|1.3.3.15|
|ELK (Logserver)|Logstash 6.1.2, Elasticsearch 6.1.2, Kibana 6.1.2|
|Jupyter Hub|0.7.0|
|Jupyter|4.3.1|
|OpenTSDB|2.3.0|
|Grafana|4.2.0|
|Anaconda|5.1.0|
|Consul|1.0.3|

## For Cloudera CDH PNDA

| Technology | Version |
|---|---|
|Hadoop (see below for components)|CM 5.9.0, CDH 5.9.0-1.cdh5.9.0.p0.23 |
|Apache Avro |avro-1.7.6+cdh5.9.0+115|
|Apache Crunch |crunch-0.11.0+cdh5.9.0+93|
|Apache Hadoop| hadoop-2.6.0+cdh5.9.0+1799|
|Apache HBase| hbase-1.2.0+cdh5.9.0+205|
|Apache Hive| hive-1.1.0+cdh5.9.0+752|
|Hue| hue-3.9.0+cdh5.9.0+3235|
|Apache Impala (Incubating)| impala-2.7.0+cdh5.9.0+0|
|Kite SDK| kite-1.0.0+cdh5.9.0+138|
|Apache Mahout| mahout-0.9+cdh5.9.0+30|
|Apache Oozie |oozie-4.1.0+cdh5.9.0+349|
|Apache Parquet |parquet-1.5.0+cdh5.9.0+176|
|Parquet-format |parquet-format-2.1.0+cdh5.9.0+14|
|Apache Pig |pig-0.12.0+cdh5.9.0+95|
|Apache DataFu |pig-udf-datafu-1.1.0+cdh5.9.0+20|
|Apache Spark |spark-1.6.0+cdh5.9.0+229|
|Apache Sqoop |sqoop-1.4.6+cdh5.9.0+77|
|Apache Sqoop2 |sqoop2-1.99.5+cdh5.9.0+40|
|Apache ZooKeeper| zookeeper-3.4.5+cdh5.9.0+98|

## For Hortonworks HDP PNDA

| Technology | Version |
|---|---|
|Apache Ambari |2.6.1.0|
|Hortonworks HDP |2.6.4.0|
|Apache Hadoop|2.7.3|
|Apache HBase|1.1.2|
|Apache Hive|2.1.0|
|Apache Mahout|0.9.0|
|Apache Oozie |4.2.0|
|Apache Pig |0.16.0|
|Apache DataFu |1.3.0|
|Apache Spark |1.6.3|
|Apache Spark |2.2.0|
|Apache Sqoop |1.4.6|
|Apache ZooKeeper|3.4.6|
|Apache Phoenix|4.7.0|
|Apache Slider|0.92.0|
|Apache TEZ|0.7.0|
