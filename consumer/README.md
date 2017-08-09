# Consumers

Kafka has a simple, clean design that moves complexity traditionally found inside message brokers into its producers and consumers.

A Kafka consumer pulls messages from one or more topics using Zookeeper for discovery, issuing fetch requests to the  brokers leading the partitions it wants to consume. Rather than the broker maintaining state and controlling the flow of data, each consumer controls the rate at which it consumes messages by maintaining an index.

Multiple consumers can be organized into groups consuming from a topic in parallel up to the number of partitions for that topic, so increasing throughput or can be individually placed into separate groups each consuming a given partition, so giving predictable ordering.

Consumers take on the name of the group to which they belong and by convention this name is organized such that conflict with other consumers is avoided.

## Data Processing consumers

For examples of how to craft data processing applications that consume from Kafka and horizontally scale using parallel processing techniques, see the next chapter on [Packages & Applications](../applications/README.md).

## Client consumers

Clients of PNDA may wish to integrate directly with Kafka instead of consuming data processed output via Impala or OpenTSDB, particularly if those clients are pre-existing applications that would otherwise integrated with directly with data sources and are designed to consume a continuous stream of data. 

In this case, you can use the [Kafka Consumer API](http://kafka.apache.org/documentation.html#newconsumerapi). Example code showing how to build a straightforward consumer using this API can be found in the [Kafka Quickstart](http://kafka.apache.org/07/quickstart.html).

We have several examples of building Kafka clients in our [example code repository](https://github.com/pndaproject/example-kafka-clients).
