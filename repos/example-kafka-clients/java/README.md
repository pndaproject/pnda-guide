# Kafka Client (Java)

## Requirements

- gradle >= 2.3

## JUnit

    $ gradle test

You can view detailed results in `build/reports/tests/classes/EncDecTest.html`.

Scope of Junit tests: test Avro encoding / decoding classes.


## Consumer

    $ gradle consumer
    $ cd build/libs
    $ java -jar consumer2tcp-0.1.jar  ../../config/consumer.properties

## Producer

    $ gradle producer
    $ cd build/libs
    $ java -jar producer-0.1.jar  ../../config/producer.properties

## Avro schema

The current implementation is working with a schema stored on a local disk.

    $ cd schema
    $ cat dataplatform-raw.avsc
    $ {"namespace": "pnda.entity",
    $  "type": "record",
    $   "name": "event",
    $   "fields": [
    $       {"name": "timestamp", "type": "long"},
    $       {"name": "src",       "type": "string"},
    $       {"name": "host_ip",   "type": "string"},
    $       {"name": "rawdata",   "type": "bytes"}
    $   ]
    $  }


Field        | Type         | Mandatory
------------ | ------------ | ------------
timestamp | long | yes event unix epoch time in milliseconds (UTC)
src | String | yes
host_ip | String | yes
rawdata | bytes | yes


## Consumer Configuration

A sample is provided in `config/consumer.properties`.

The properties you can edit are:
     
    zookeeper.connect            => Zookeeper connection string, comma separated host:port pairs
    group.id                     => Unique Consumer Group
    topic                        => Put the topic name or topic filter (avro.log.*)
    avro.schema                  => reference to the local avro schema
    output.host                  => TCP target IP address
    output.port                  => TCP target port
     
## Producer Configuration

A sample is provided in `config/producer.properties`.

The properties you can edit are:

    metadata.broker.list                 => kafka brokers used for bootstrapping, format: host1:port1,host2:port2 ...
    group.id                             => Unique Producer Group
    topic                                => A unique topic name (ex. avro.log.esc)
    avro.schema                          => reference to the local avro schema
    dataplatform.message.src             => Message source for populating src field in avro record
    dataplatform.message.host_ip.fromnic => desired network interface to be used for populating host_ip field in avro record
    dataplatform.message.host_ip.format  => desired IP address format: v4 or v6
    dataplatform.message.host_ip.default => Set a default IP for host_ip instead of NIC usage
    
## Play with the PNDA Producer

On succesful startup, the proposed producer reads STDIN input, writes into a Kafka topic.
By default, avro encoding is ON and all messages will be avro+binary encoded. This happens when we
configure the PNDA producer with our custom encoder as set in `props.put(""+Constants.PROP_PRODUCER_MSG_ENCODER,Constants.KAFKA_DATAPLATFORM_MSG_ENCODER)` in `AProducer.java` file.
Logs are display on the default stdout destination.

## Play with the PNDA Consumer

The Consumer initialization steps takes place in `Consumer2Tcp.java`: `main()` reads the configuration file and
instantiates a Consumer2Tcp object that creates a Kafka connector, with a configuration set in `createConsumerConfig()`.
When run() is called, we init the consumer with a filter to specify a pattern for subscribing to 1 or more topics in `createMessageStreamsByFilter()`.
On succesful startup, the proposed consumer is listening for incoming messages on subscribed kafka topics.
Before trying to read messages, the consumer tries to check the TCP target is alive by connecting to it in `ConsumerProcess/externalTcpConnect()`. If the connection fails, no read happens (because reading implies a move of the kafka log head offset and could imply messages lost).
When a message is read, if avro decoding is to ON (default), the message is decoded and then sent to a TCP target as defined by configuration and implemented in `ConsumeProcess/sendToExternalTcp()`.
If it is required to perform actions on received messages before being forward to the TCP target, we have highlighted the section where it should take place in ConsumerProcess. You can search for tag #CustomImplementation# in ConsumerProcess.java source code.

Note: for testing purposes, if you set the output.host field in the configuration file to an empty string, then the consumer will not forward messages to the tcp target but will display them on the standard output of the process.

## Producer and Partition

By default, it uses the random partitionner. Let the key empty when you play with the producer.
If you put the option partitioner.class=com.cisco.formatter.SimpleKeyPartitioner, then you can enter the following string:

- string digit to be converted into an integer ("123" converted to 123)
- string with dot separator and last split made of digits (ex.: "92.164.34.56" => 56 extracted)

Then a modulo with the total number of partitions for the selected topic is done and used to select the partition to write to. This can be use when strict ordering is required according to a producer strategy.

## TCP server target for PNDA consumer

An easy way to simulate a TCP server target :

    $ nc ::1 -l 2345

## Warning: group id

The reader shall be familiar with Kafka. A topic may be divided into more than one partition. Using the kafka high level API, a consumer shall be identified by a group id unique to the application context. 
The number of partitions for a topic defines the maximum number of consumers inside a group.
A consumer group is used to isolate a group of consumers for reading a dedicated topic and partitions. Example: 3rd party vendor's system could launch 2 consumer with the same group id. Consumers rebalance themselves for partitions. If two different groups use the same id, some of the consumers will not receive data.