# Kafka Client (PHP)

Please note that this tool can be used for unit testing, but should not be used in production.

## Default Settings

* default topic: avro.log.asa
* kafka host: 127.0.0.1:9092
* zookeeper host: 127.0.0.1:2181

## Requirements

* [PHP](http://www.php.net)
* [phpKafka](https://github.com/EVODelavega/phpkafka)
* [avro](https://github.com/johnj/php5-xcom/blob/master/README.md#libavro)
* [zookeeper](https://github.com/andreiz/php-zookeeper)

## Disclaimer

These files are a simple illustration of a producer and consumer written in PHP, and were used to test the reference Java-based implementation for Avro binary encoding and decoding.

## Consumer

Start a consumer (raw message):

    php consumer.php 
