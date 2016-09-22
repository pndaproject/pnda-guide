# PNDA Logstash Avro codec plugin

## About 

[Logstash](https://www.elastic.co/products/logstash) is a lightweight, open source data collection engine organized as simple pipeline with a large number of plugins. It has input plugins for Netflow, SNMP, collectd, syslog, etc. 

Logstash is used as to collect, enrich and transport data from multiple sources into PNDA.

This codec plugin is used as a stream filter for deserializing or serializing inidvidual logstash events in accordance with the PNDA avro schema.

At a very high level, 

* Kafka Producer will use this codec to avro encode input events.
* Kafka Consumer will use this codec to avro decode events read from the data bus.

## Avro encapsulation

The Avro schema is as follows:
```json
    {
        "namespace": "pnda.entity",
        "type": "record",
        "name": "event",
        "fields": [
            {"name": "timestamp", "type": "long"},
            {"name": "src",       "type": "string"},
            {"name": "host_ip",   "type": "string"},
            {"name": "rawdata",   "type": "bytes"}
        ]
    }
```

Events should be encoded as Avro fragments. This means that the schema is not included in each event, but is instead made available at encode/decode time.

Explanation of the fields:

* `timestamp`: Timestamp of when the event was generated/ingested by PNDA.
* `src`: source of the event. Where does the event come from? (e.g. syslog, collectd)
* `host_ip`: source IP address of the machine where event is generated/ingested by PNDA
* `rawdata`: The 'raw' data goes here.

## Installation

We recommend logstash 2.3.4, the latest version at the time of writing.

### Prerequisites

Install Java (if you haven't done so):
```sh
    sudo add-apt-repository ppa:webupd8team/java
    sudo apt-get update
    sudo apt-get install oracle-java8-installer
```

**Important:** Install ruby 2.x using rvm.
```sh
    gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
    curl -sSL https://get.rvm.io | bash -s stable --ruby
```

Follow the instruction this produces (in your case the location will be different):
 
  * To start using RVM you need to run `source /home/cloud-user/.rvm/scripts/rvm`
    in all your open shell windows, in rare cases you need to reopen all shell windows.
    
Create the source directory:
```sh
    mkdir -p /opt/logstash
    cd /opt/logstash
```
### Build avro codec

First, clone this repository into the source directory.
```sh
    cd /opt/logstash
    git clone git@github.com:pndaproject/prod-logstash-codec-avro.git
```

Build the gem:
```sh 
    cd /opt/logstash/prod-logstash-codec-avro
    gem build pnda-logstash-codec.gemspec
```

You should see a `Successfully built RubyGem` message.

### Install logstash 2.3.4
```sh
    cd /opt/logstash
    wget https://download.elastic.co/logstash/logstash/logstash-2.3.4.tar.gz
    tar -zxvf logstash-2.3.4.tar.gz
```

### Add avro codec to logstash
```sh
    cd /opt/logstash/logstash-2.3.4
    bin/logstash-plugin install /opt/logstash/prod-logstash-codec-avro/logstash-codec-platformavro-0.1.0.gem
```

You should see an `Installation successful` message.

Finally, check if everything went well:
```sh
    bin/logstash-plugin list | grep platformavro
```

Which should display:
```sh
    logstash-codec-platformavro (0.1.0)
```

If you were already running Logstash, restart it. Now you are ready to proceed.

## Example Configuration

As an example, we will see how to read TCP data and send it to PNDA.

**Note:** This assumes you have created a kafka topic named `test` and you are ready to direct data towards this topic.
```properties

    input {
      tcp {
         port => 20518
         add_field => [ "src", "syslog" ]
        }
    }

    filter {
    }

    output {
      stdout { codec => rubydebug }
      kafka {
        bootstrap_servers => '127.0.0.1:9092'
        topic_id => 'test'
        value_serializer => "org.apache.kafka.common.serialization.ByteArraySerializer"
        codec => platformavro {}
      }
    }
```

Save the above configuration in `logstash.conf`.

Then run logstash:
```sh
    cd logstash-2.3.4/
    bin/logstash agent -f logstash.conf
```

You should see a `Logstash startup completed` message printed on the console. If you don't see this message, something went wrong in your setup or there's an error in your config.

In another terminal:
```sh
    echo '{"router: Jun 19 09:56:14.258 EDT: %LINEPROTO-5-UPDOWN: Line protocol on Interface Tunnel1, changed state to down"}' | nc localhost 20518
```

Start kafka consumer (if you are using local kafka cluster):
```sh
    cd $KAFKA_HOME
    bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic test --from-beginning
```

You should be able to see the syslog event we just sent.

## SSL Configuration

In case you would like to work with a secured Kafka cluster, you will need to install a newser version of the kafka output plugin by running:

```sh
    bin/logstash-plugin install --version 3.0.0 logstash-output-kafka
```

The logstash.conf configuration file should be for example:
```properties
input {
  tcp {
     port => 20518
     add_field => [ "src", "syslog" ]
    }
}

filter {
}

output {
  stdout { codec => rubydebug }
  kafka {
    bootstrap_servers => '127.0.0.1:9093'
    topic_id => 'avro.log.localtest'
    value_serializer => "org.apache.kafka.common.serialization.ByteArraySerializer"
    codec => platformavro {}
    ssl => true
    ssl_keystore_location => "/òpt/pnda/server.keystore.jks"
    ssl_keystore_password => "test1234"
    ssl_truststore_location => "/opt/pnda/server.truststore.jks"
    ssl_truststore_password => "test1234"
  }
}
```

Check the [Logstash Kafka output plugin documentation](https://www.elastic.co/guide/en/logstash/master/plugins-outputs-kafka.html) and the security part on the [PNDA Guide](http://pnda.io/guide) for more information.

## Field mapping

Let's understand a bit more about how input fields are mapped to fields expected by the codec.

In Logstash world, the message field is like a default field. It's where most input plugins place the payload that they receive from the network, read from a file, etc.

The way our codec works is to map logstash event fields to the avro schema:
 
-  avromsg["src"] is the value from event["src"]
-  avromsg["host_ip"] is the value from event["host"]
-  avromsg["timestamp"] is the value from event["@timestamp"] in milliseconds
-  avromsg["rawdata"] is the value from event["message"]

**Note** : `timestamp` and `host` fields are likely generated by logstash itself.

The `event["host"]` value is parsed by splitting IP address and port (v4, v6). The method `parse_addr` method in `lib/logstash/codecs/platformavro.rb` handles cases where the host field might be a combination of IP address and/or port.  

Note that `src` and `message` are not default fields in logstash event. They need to be set with filters or by an input plugin.

In simple terms, we expect the Logstash event after it's been through the filter section to contain the fields `src`, `host`, `@timestamp` and `message`. How they are set is up to the integrator.

For example, let's take the `file` input plugin. Here's a sample config:
```properties
    input {
        file {
            path => "/var/log/*"
            add_field => [ "src", "system_logs" ]
        }
    }
```

Here's a sample line:

    Mar 23 13:17:01 localhost CRON[25941]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)
    
If you want to send the whole line as is, you could do that. 

The output from logstash might look something like this:
```properties
    {
        "timestamp" => 1458740254000,
        "src" => "system_logs",
        "host_ip" => "192.168.10.1",
        "rawdata" => "Mar 23 13:17:01 localhost CRON[25941]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)"
    }
```

As you may have guessed, the whole `message` field becomes part of `rawdata`.

These fields are sent to the Kafka output plugin.

You can perform quite complex logic inside a logstash filter to do whatever you need to do.

For more information, see the [logstash](https://www.elastic.co/guide/en/logstash/2.3/index.html) documentation.

## Testing

To run unit tests, download jruby via rvm:
```sh
    cd prod-logstash-codec-avro
    curl -sSL https://get.rvm.io | bash -s stable --ruby=jruby
```

Install bundle gem:
```sh
    jruby -S gem install bundler
```

Update dependencies and run tests:
```sh
    jruby -S bundle install
    bundle exec rspec
```