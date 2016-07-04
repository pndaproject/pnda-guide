# Developing a Producer 

In many circumstances, it may be advantageous to create your own producer: for example, if you have a data source not already handled by Logstash or Open Daylight, or if you wish to exercise more control over the way data is produced into the platform.

In this case, you can use the [Kafka Consumer API](http://kafka.apache.org/documentation.html#newconsumerapi). Example code showing how to build a straightforward consumer using this API can be found in the [Kafka Quickstart](http://kafka.apache.org/07/quickstart.html).

We have several examples of building Kafka clients in our [example code repository](../repos/example-kafka-clients/README.md).



