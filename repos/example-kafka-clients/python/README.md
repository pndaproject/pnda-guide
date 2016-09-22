# Kafka Client (Python)

Please note that this tool can be used for unit testing, but should not be used in production.

## Default Settings

* default topic: raw.log.localtest or avro.log.localtest
* kafka host: localhost:9092 or localhost:9093 for secured brokers using SSL

## Requirements

* python 2.7.4
* pip modules
    * kafka-python (1.3.1)
    * avro (1.7.7)
    * optional for random text: loremipsum (1.0.5)

## Disclaimer

These files are a simple illustration of a producer and consumer written in Python, and were used to test the reference Java-based implementation for Avro binary encoding and decoding.

## Kafka / Zookeeper default settings

* raw  option - topic is raw.log.localtest
* avro option - topic is avro.log.localtest
* Work with a local kafka (localhost:9092 or localhost:9093)
* Edit the python file for others hosts settings

## Consumer

Start a consumer (raw message):

    python consumer.py 

Start a consumer (serializer - avro):

    python consumer.py -s true

Start a consumer (serializer - avro and extra header matching confluent.io scheme id)

    python consumer.py -s true -e true

Same with a secured kafka cluster

	python consumer-ssl.py 

## Producer

Start a producer (avro serializer is always on):

    python producer.py

Start a producer (avro serializer is always on - extra header):

    python producer.py -e true

Start a producer (avro serializer is always on - loop mode):

    python producer.py -e true -l true

Start a producer with a secured kafka cluster

	python producer.py -z