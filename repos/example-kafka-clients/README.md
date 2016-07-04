# Example Kafka Clients

This project contains examples of Kafka client code for writing to and reading from the PNDA Kafka databus. The Java example ilustrates how to work with the databus, and can be shared with anyone who is interested in producing PNDA messages to the databus or reading PNDA messages from the databus. Tools written in Python and PHP are also provided for unit testing, but should not be used in production. 

## [Java](java/README.md) 

The code can be compiled into a self-executing `jar` file.

 - Producer
 - Consumer (based on Kafka high level API)
 - Build / config instructions provided in the java directory

## [Python](python/README.md) 

This tool can be used for validating Avro serialization with python. Note that this tool is for unit testing only, and should not be used in production. 

 - Producer
 - Consumer
 - Tips / Usage instruction provided in the pyhton directory

## [PHP](php/README.md) 

This tool can be used for validating Avro serialization with PHP. Note that this tool is for unit testing only, and should not be used in production. 

 - Consumer

## Kafka

 - Tools developed with kafka 0.9.0.0
 - Integration done with the following build reference:
   - kafka_2.10-0.9.0.0.tgz (http://apache.claz.org/kafka/0.9.0.0/kafka_2.10-0.9.0.0.tgz)
   - OS: ubuntu 14.04 64 bits
