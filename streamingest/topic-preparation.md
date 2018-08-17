# Preparing topics

* [Kafka topic creation](#kafka-topic-creation): Explains the different options for creating Kafka Topics
* [Gobblin topic configuration](#gobblin-topic-configuration): Explains the default and more advances ingest mechanics and associated configurations

## Kafka topic creation

Kafka in PNDA comes with different options for creating topics, each with their respective strengths and limitations as will be explained in this section:
1) [Kafka Manager UI](https://github.com/yahoo/kafka-manager/blob/master/README.md)

   This option is by far the easiest to use as it comes pre-configured for your cluster. It is consequently very helpful for exploring the current setup and getting familiar with the configuration needed for using the alternative tools mentioned below. The Kafka Manager UI is linked to from the [PNDA console](../console/images/metrics_all_green.png) and has a tab [Cluster->Summary](images/cluster_config.png) which reveals the Zookeepers configuration. The drawback of the Kafka Manager is that it doesn't expose a user friendly API that permits topic creation. Indeed, while the UI itself exploits a back-end API, this API embeds the response in a web page which doesn't allow for a easy programmatic approach. 
   ```
   > export TOPIC=json.telemetry
   > curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "configs[0].name=retention.ms&configs[0].value=&configs[1].name=max.message.bytes&configs[1].value=&configs[10].name=flush.ms&configs[10].value=&configs[11].name=cleanup.policy&configs[11].value=&configs[12].name=file.delete.delay.ms&configs[12].value=&configs[13].name=segment.jitter.ms&configs[13].value=&configs[14].name=index.interval.bytes&configs[14].value=&configs[15].name=compression.type&configs[15].value=&configs[16].name=segment.ms&configs[16].value=&configs[17].name=unclean.leader.election.enable&configs[17].value=&configs[2].name=segment.index.bytes&configs[2].value=&configs[3].name=segment.bytes&configs[3].value=&configs[4].name=min.cleanable.dirty.ratio&configs[4].value=&configs[5].name=min.insync.replicas&configs[5].value=&configs[6].name=delete.retention.ms&configs[6].value=&configs[7].name=flush.messages&configs[7].value=&configs[8].name=preallocate&configs[8].value=&configs[9].name=retention.bytes&configs[9].value=&partitions=2&replication=1&topic=$TOPIC" http://sl-4553-16237-kafka-0:10900/clusters/sl-4553-16237/topics/create
   <!DOCTYPE html>
   <html>
   <head>
      <title>Create Topic</title>
   ...
   <div class="alert alert-success" role="alert">Done!</div>
   ...
   </html>
   ```
2) Kafka CLI

   This option requires access to the Zookeepers at this moment, but allows for a more automated way of creating topics.
   With the knowledge of the zookeeper location, the CLI can be used for creating topics from one of the Kafka nodes using the following command:
   ```
   > cd /opt/pnda/kafka/
   > cat config/server.properties | 'fgrep zookeeper.connect='
   zookeeper.connect=sl-4553-16237-kafka-0:2181
   > bin/kafka-topics.sh --create --zookeeper sl-4553-16237-kafka-0:2181 --replication-factor 1 --partitions 1 --topic json.telemetry
   ```
   We can also use the same CLI to see that topic being present:
   ```
   > bin/kafka-topics.sh --list --zookeeper sl-4553-16237-kafka-0:2181
   json.telemetry
   ```
   as will as the detailed configuration:
   ```
   > bin/kafka-topics.sh --zookeeper sl-4553-16237-kafka-0:2181 --describe --topic json.telemetry
   Topic:json.telemetry  PartitionCount:1  ReplicationFactor:1 Configs:
     Topic: json.telemetry Partition: 0  Leader: 0 Replicas: 0 Isr: 0
   ```
   Although this CLI allows for a more programmatic approach, it requires access to the zookeeper (which is usually not exposed outside the cluster but could be tackled in a coarse manner using `ssh [user@]hostname [kafka CLI]`).
 
3) Kafka Client API

   The last option consists in using the Java [AdminClient](https://kafka.apache.org/documentation/#adminapi) API. The AdminClient API supports managing and inspecting topics, brokers, acls, and other Kafka objects. As the name suggest, these are language specific API's, yet provide the best programmatic approach available at the moment.   In addition, these API's don't require access to Zookeeper but interact directly with the Kafka brokers through the [wire protocol implemented in Kafka](https://kafka.apache.org/protocol).
   See [demo app](https://raw.githubusercontent.com/simplesteph/kafka-0.11-examples/master/src/main/scala/au/com/simplesteph/kafka/kafka0_11/demo/KafkaAdminClientDemo.scala) for an example usage of this API. 

## Gobblin topic configuration

### Introduction
PNDA supports any data type and it is normally not necessary to prepare data for ingestion by the platform. In order to create consistency of the data stored on the platform, all data adheres to one and the same Avro schema irrespective of serialization method being used.

We will call this Avro schema the PNDA schema in our further documentation:

	{
	  "namespace": "pnda.entity",
	  "type": "record",
	  "name": "event",
	  "fields": [
	     {"name": "timestamp", "type": "long"},
	     {"name": "source",    "type": "string"},
	     {"name": "rawdata",   "type": "bytes"}
	  ]
	}

#### Explanation of the PNDA schema fields:
* `timestamp`: Timestamp of when the event was generated/ingested by PNDA, **in milliseconds**.
* `source`: source of the event. Where does the event come from? (e.g. syslog, collectd) it could be a high level abstracted source or simple the topic name.
* `rawdata`: The 'raw' data goes here, it's an array of **bytes** and is not interpreted by PNDA in any way.

In the following sections, we will go over the different use cases that are supported by the platform:
* [Un-configured topic](#un-configured-topic): Default behavior when no information is provided about the data serialization.
* [Matching schema topic](#matching-schema-topic): topic configured with Avro de-serialization and an Avro schema identical to the one above.
* [Avro configured topic](#avro-configured-topic): topic configured with an Avro de-serialization and an Avro schema different than the one above.
* [Protobuf configured topic](#protobuf-configured-topic): topic configured with a Protobuf serialization. 

Topic configurations themselves are currently tightly coupled to Gobblin and are stored directly in a [Gobblin Job Configuration File](https://gobblin.readthedocs.io/en/latest/user-guide/Working-with-Job-Configuration-Files). This configuration file allows to define configurations targeted to specific topics through the usage of a regular expression matching the topic name in the dataset field as explained in details in the [Topic Specific Configuration](https://gobblin.readthedocs.io/en/latest/case-studies/Kafka-HDFS-Ingestion/#topic-specific-configuration) section. You can find the default configurations in the [PNDA Gobblin Configuration file](https://github.com/pndaproject/platform-salt/blob/develop/salt/gobblin/templates/mr.pull.tpl). In addition to the Gobblin specific configuration fields, our platform also supports [PNDA specific configuration fields](https://github.com/pndaproject/platform-gobblin-modules/blob/bd88d4d1a6b2462d674b5a840bdc09e5817a4f20/src/main/java/gobblin/pnda/registry/GobblinConfigRegistry.java#L27). Adding different configurations for new topics is just a matter of extending the JSON formatted value of the `kafka.topic.specific.state` field. The configuration file is located (at /opt/pnda/gobblin/configs/mr.pull) on the node with the gobblin role (use `sudo salt --output newline_values_only  -C 'G@roles:gobblin' cmd.run hostname` from within the luster to locate the node).

The final section will provide details about the file location and format when storing the ingested data and explain how to detect misconfiguration:
* [Dataset format & location](#dataset-format--location)

### Un-configured topic

With no additional configuration, each datum is treated the same way and will get serialized as a simple Avro schema fragment compliant to the schema described above. 
Compliance to as same PNDA schema is then achieved by adding a logical source and a timestamp to the datum which will get stored in the rawdata. The two extra fields that come to complement the payload will hold data generated by the platform itself as follows: 
1) The name of the topic at which the data is targeted will serve as the source field.
2) The ingest time of the data from the topic will serve as the timestamp field. 

Now, while this is by far the simplest process to start exploring the capabilities of the platform, it is important to understand the limitation of this use case:
* First, it is important to note that the ingest time by the platform is different than the production of the datum itself. As expected, this time difference is partially due to the travel time of the datum out of it's source over the network to the Kafka broker. But what may be less trivial is that the platform itself is also responsible for a considerable delay. Indeed, the ingest process itself is running as a batch process on a fixed schedule delaying even further this ingest timestamp depending directly on the ingest frequency setting. For an ingest frequency of 30 minutes, this effect could introduce a delay of up to an hour.
* Another characteristic of the platform is that data originating from a same source get stored under a same directory structure based on the source field value. Without further configuration, each datum originating from the same topic gets a source field assigned to the same topic. This results in all the data from a same topic to be stored under a same directory tree without then leveraging the de-multiplexing capability of the platform.

In order to remediate these shortcomings, support for configuration has been added to the platform enabling the more advanced use cases documented next.   

### Matching schema topic

As some will have already anticipated, when data being streamed to the platform already happens to adhere to the PNDA schema, adding the extra fields mentioned above may just be overhead and the limitations mentioned above may just not acceptable for the use case at hand. Consequently, the platform has been optimized for this scenario by 1) allowing the data be stored without adding the extra fields and 2) by recognizing the already existing fields during the storage phase. 

As this is a pretty common scenario, our platform comes with a predefined and preconfigured topic group. So, let's use that [example](https://github.com/pndaproject/platform-salt/blob/develop/salt/gobblin/templates/mr.pull.tpl) to introduce and explain the different configuration fields (slightly reordered for convenience):
```
  {
    "dataset": "avro.pnda.\*",
    "pnda.family.id": "avro.pnda",
    "pnda.converter.delegate.class": "gobblin.pnda.PNDAAvroConverter",
    "pnda.avro.schema": '{"namespace": "pnda.entity","type": "record","name": "event","fields": [ {"name": "timestamp", "type": "long"}, {"name": "source", "type": "string"}, {"name": "rawdata", "type": "bytes"}]}',
    "pnda.avro.timestamp.field": "timestamp",
    "pnda.avro.source.field": "source"
  }
```

#### Explanation of the `avro.pnda.*` fields:
* The `dataset` field is evaluated to match a specific topic name and allows for regular expressions as supported by the [Java regex Pattern](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html). For our example above, "dataset" : "avro.pnda.\\\*", all topics whose name matches the pattern `avro.pnda.*` will have all the enclosed configuration properties applied. An example such topic name would be: `pnda.avro.snmp`. If more than one topic matches multiple datasets then the properties from all the JSON objects will be will be applied.

The next two fields are common to all topic configurations:
* The `pnda.family.id` field is not used during the data processing but will embedded in the file produced as part oftheingest process.
* The `pnda.converter.delegate.class` field identifies which Converter class should be used to process the data. Converter classes are named after the serialization format they can handle. For our example above, "pnda.converter.delegate.class": "gobblin.pnda.PNDAAvroConverter", the converter can handle Avro de-serialization events only.

The remaining fields, all starting with `pnda.avro`, are specific to the Converter class:
* The `pnda.avro.schema` field defines the Avro schema to be used during the parsing of the data and needed to locate and interpret the values corresponding to the keys defined in the next two fields. For our example above, one can notice this schema is identical to the PNDA Avro schema (described in the [Introduction](#introduction) section above).
* The `pnda.avro.timestamp.field` identifies the name of the field in the `pnda.avro.schema` that should serve as the value of the `timestamp` field (in the PNDA schema) of the datum produced by the ingest process. For our example above, one can notice this field name is identical to the field name in the PNDA Avro schema that contains the timestamp value of the datum (described in the [Introduction](#introduction) section above).
* The `pnda.avro.source.field` identifies the name of the field in the `pnda.avro.schema` that should serve as the value of the `source` field (in the PNDA schema) of the datum produced by the ingest process. For our example above, one can notice this field name is identical to the field name in the PNDA Avro schema that contains the source value of the datum (described in the [Introduction](#introduction) section above).

In short, these four last configuration field settings together inform the ingest process that the data under the associated topics already conforms to the PNDA schema and hence no further datum manipulation is required to enforce schema consistency when stored on the platform. Streaming already compliant data can be used to offload the conversion work outside the PNDA cluster and to tackle conversions currently not supported.

Please refer to our [Data Preparation](data-preparation.md) Guide to understand how to encapsulate data compliant with to the PNDA schema.

### Avro configured topic

Avro is a broadly used serialization framework and chances are high that your data-source or Kafka producer already supports streaming Avro serialized data. But what may be less likely, is that your data-source can be configured to stream data that complies with the PNDA schema.

In this scenario, the ingest framework will have to augment the incoming datum to conform with the PNDA scheme, but will again leave the incoming datum unaltered by storing the original content in the rawdata field. The additional `timestamp` and `source` fields on the other hand, can be configured to contain data from the original content. It is not required to configure both those fields, in which case, they will default to values as in the [Un-configured topic]() scenario, i.e. the `source` will default to the topic name and the `timestamp` will default to the ingest time.

But for the fields that an equivalent field in the data, a mapping can be configured to extract the value out of the datum and copy re-use it for the associated PNDA schema field. We'll use and example again to illustrate this configuration. 

Consider the following schema as the Avro schema the streamed data complies to:
``` 
{
  "namespace": "snmp.entity",
  "type": "record",
  "name": "r",
  "fields": [
     {"name": "r1", "type": "bytes"},
     {"name": "r2", "type": "float"},
     {"name": "r3", "type": "string"},
     {"name": "r4", "type": "float"},
     {"name": "r5", "type": "long"},
     {"name": "r6", "type" : {
          "name" : "l",
          "type" : "record",
          "fields" : [
             {"name" : "l1", "type" : "long"},
             {"name" : "l2", "type" : "string"}
           ]}
     }
  ]
}
```
with a topic name: `avro.my.flow`

and associated configuration:

```
  {
    "dataset": "avro.my.flow",
    "pnda.family.id": "avro_my_flow",
    "pnda.converter.delegate.class": "gobblin.pnda.PNDAAvroConverter",
    "pnda.avro.schema": '{ "namespace": "snmp.entity", "type": "record", "name": "r", "fields": [ {"name": "r1", "type": "bytes"}, {"name": "r2", "type": "float"}, {"name": "r4", "type": "string"}, {"name": "r6", "type": "float"}, {"name": "r3", "type": "long"}, {"name": "r7", "type" : { "name" : "l", "type" : "record", "fields" : [ {"name" : "l1", "type" : "long"}, {"name" : "l2", "type" : "string"} ]}}]}, 
    "pnda.avro.timestamp.field": "r5",
    "pnda.avro.source.field": "r3"
  }
```

#### Explanation of the `avro.my.flow` settings:
* The `dataset`: Applies this configuration to all the data originating from the `avro.my.flow` topic.
* `pnda.family.id`: Insert a meta-data property in the Avro file header map with as key: `pnda.family.id` and as value the associated string value from the configuration, i.e. `avro_my_flow`. This field is mandatory.
* `pnda.converter.delegate.class`: De-serializes the datum using the specified PNDA Converter class, i.e. `gobblin.pnda.PNDAAvroConverter`, parsing the datum as an [Avro](https://avro.apache.org) fragment interpreting the datum according to the subsequent fields. This field is mandatory.
* `pnda.avro.schema`: Defines the Avro schema to use for parsing the datum. This field is mandatory for the `gobblin.pnda.PNDAAvroConverter` converter.
* `pnda.avro.timestamp.field`: Defines the name of the field of the schema definition above that should serve as timestamp value when composing the PNDA datum to store on the platform. This field is optional. If left out, the ingest time will serve as the value.
* `pnda.avro.source.field`: Defines the name of the field of the schema definition above that should serve as source value when composing the PNDA datum to store on the platform. This filed is optional. If left undefined, the topic name will serve as value.

So a datum stored on the platform originating from the `avro.my.flow` topic would, as expected, be serialized as an Avro binary format conform to the PNDA Schema and have its successive field set as follows:
* The `timestamp` field would contain a copy of the input datum field `r5`
* The `source` field would contain a copy of the input datum field `r3`
* The `rawdata` field would contain the whole input datum datum as originally streamed to the topic, i.e. still Avro serialized

#### Limitations
* At the moment, the uniqueness of the `pnda.family.id` has to be ensured by the user. When adding a new configuration, care should be taken to select a different `pnda.family.id` for different configurations. 
* Both the `timestamp` and the `source` fields can **not** be nested in the schema definitions, i.e. the field l1 can serve as `timestamp` and the field l2 can **not** serve as `source`.
* The value corresponding to the `timestamp` field will be interpreted as the value of time in **milliseconds** since 0 hours, 0 minutes, 0 seconds, January 1, 1970, Coordinated Universal Time, without including leap seconds.

### Protobuf configured topic

Another broadly spread serializer for which support has been added to the PNDA ingest framework is [Protocol Buffer](https://developers.google.com/protocol-buffers/) (Protobuf). This highly efficient serialization and compression format is increasingly used in network devices to continuously to stream real time configuration and operating state information. Examples usage in open source streaming telemetry tools include [ygot](https://github.com/openconfig/ygot) and [pipeline](https://github.com/cisco/bigmuddy-network-telemetry-pipeline). As streamed telemetry is another important enabler for operational network analytics, a default example configuration has been included in the platform. This example configuration will be used to document the Protobuf specific settings based on pipeline which can be configured to stream telemetry data directly to the PNDA Kafka brokers. 

When configured to output Protobuf serialized data, each datum conforms to the [protocol buffer message type](https://github.com/cisco/bigmuddy-network-telemetry-proto/blob/01b5f4349499a554c5ce28d904a5cf1e12b0f2a0/staging/telemetry.proto) defined in the pipeline project. In this message type, 2 fields are of particular interest:
1) the `node_id_str`: a string encoded unique node ID of the MDT-capable device producing the message.
2) the `msg_timestamp`: the time when the data encoded in the Telemetry message is generated - encoded as milliseconds since the epoch.

So for this scenario, data includes two fields at the respective tags:
```
string node_id_str = 1;
uint64   msg_timestamp = 10;
```

and is streamed to a topic with name:
`protobuf.telemetry.flow`

and associated configuration:
```
  {
    "dataset": "protobuf.telemetry.\*",
    "pnda.converter.delegate.class": "gobblin.pnda.PNDAProtoBufConverter",
    "pnda.family.id": "protobuf.telemetry",
    "pnda.protobuf.timestamp.tag": "10",
    "pnda.protobuf.source.tag": "1"
  }
```

#### Explanation of the `protobuf.telemetry.*` settings:
* The `dataset` field is evaluated to match a specific topic name and allows for regular expressions as supported by the [Java regex Pattern](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html). For our example above, "dataset" : "protobuf.telemetry.\\\*", all topics whose name matches the pattern `protobuf.telemetry.*` will have all the enclosed configuration properties applied. An example such topic name would be: `protobuf.telemetry.flow`. If more than one topic matches multiple datasets then the properties from all the JSON objects will be will be applied.

The next two fields are common to all topic configurations:
* The `pnda.family.id` field is not used during the data processing but will embedded in the file produced as part of the ingest process.
* The `pnda.converter.delegate.class` field identifies which Converter class should be used to process the data. Converter classes are named after the serialization format they can handle. For our example above, "pnda.converter.delegate.class": "gobblin.pnda.PNDAProtoBufConverter", the converter can handle Protocol Buffer [version 3](https://developers.google.com/protocol-buffers/docs/overview#introducing-proto3) de-serialization events only.

The remaining fields, all starting with `pnda.protobuf`, are specific to the Converter class:
* The `pnda.protobuf.timestamp.tag` identifies the tag of the field in the Protobuf message that should serve as the value of the `timestamp` field (in the PNDA schema) of the datum produced by the ingest process. For our example above, one can notice that this tag matches the field that corresponds to the msg_timestamp in the [protocol buffer message type](https://github.com/cisco/bigmuddy-network-telemetry-proto/blob/master/staging/telemetry.proto#L72).
* The `pnda.protobuf.source.tag` identifies the tag of the field in the Protobuf message that should serve as the value of the `source` field (in the PNDA schema) of the datum produced by the ingest process. For our example above, one can notice that this tag matches the field that corresponds to the node_id_str in the [protocol buffer message type](https://github.com/cisco/bigmuddy-network-telemetry-proto/blob/master/staging/telemetry.proto#L36).

So a datum stored on the platform originating from the `protobuf.telemetry.flow` topic would, as expected, be serialized as an Avro binary format conform to the PNDA Schema and have its successive field set as follows:
* The `timestamp` field would contain a copy of the input datum field value corresponding to the `msg_timestamp`
* The `source` field would contain a copy of the input datum field value corresponding to the `node_id_str`
* The `rawdata` field would contain the whole input datum as originally streamed to the topic, i.e. still Protobuf serialized

#### Limitations

* At the moment, the uniqueness of the `pnda.family.id` has to be ensured by the user. When adding a new configuration, care should be taken to select a different `pnda.family.id` for different configurations. 
* Both the `timestamp` and the `source` fields can **not** be nested in the schema definitions.
* The value corresponding to the `timestamp` field will be interpreted as the value of time in **milliseconds** since 0 hours, 0 minutes, 0 seconds, January 1, 1970, UTC, without including leap milliseconds.

### Dataset format & location

#### Dataset format
When persisting data onto the platform, PNDA also exploits the [Avro object container file format](https://avro.apache.org/docs/1.2.0/spec.html#Object+Container+Files). Besides being a natural container format for holding the Avro objects generated by the platform, it also defines a header format. This header contains metadata properties (including `avro.schema` and `avro.codec`) allowing for dynamic typing. Indeed, using this container, data is always accompanied by its object schema and other relevant properties (codec, count, sync) that allow for generic data-processing systems. The PNDA platform continues the same paradigm by including additional PNDA specific properties in the same header but under a new `pnda.*` namespace to avoid future collision.

##### PNDA defined metadata header properties:

* `pnda.family.id`: This property is set to the value of the same property defined in the Gobblin configuration file. It allows to look up the configuration that was used to produce the objects in the same dataset. i.e. A generic data-processing systems can herewith look up the configured serialization of the rawdata and additional parsing information. As such, care should be taken to define different `pnda.family.id` for different configurations and not re-use a `pnda.family.id` with a different configurations over time. Consequently, it is recommended to keep track of `pnda.family.id` values over time, even when topics get deleted.
* `pnda.field.timestamp.extracted`: This property is either set to True when the Gobblin configuration file defined sufficient and valid information to extract a timestamp value from the input record and set to False otherwise.
* `pnda.field.source.extracted`: This property is either set to True when the Gobblin configuration file defined sufficient and valid information to extract a source value from the input record and set to False otherwise.

#### Dataset location

When persisting data onto the platform, PNDA persists the datasets on HDFS in a subdirectory tree under the folder defined in the [PNDA salt pillar](https://github.com/pndaproject/platform-salt/blob/develop/pillar/pnda.sls) (see `pnda.master_dataset.directory`).

A datum that has successfully been parsed and for which the `pnda.field.source.extracted` was set will be further partitioned under a sub directory structure starting `source=` followed by the value extracted the source field of the datum. For example: <pnda.master_dataset.directory>/source=<router1>/, <pnda.master_dataset.directory>/source=<router2>/, ...

A datum that has successfully been parsed but for which the `pnda.field.source.extracted` was **not** set will be stored under a sub directory structure starting with `topic=` followed by the name of the topic it was consumed from.
Next, data will be further partitioned based on the value of the `timestamp` field. Starting from the year, then the month, the day and finally the hour.

##### Misconfigured topic

Misconfiguration does not always lead to execution exceptions. But when it does, the platform will quarantine the data. Such a situation can occur when for example the field holding the timestamp value can **not** be parsed as a long. Data that has been quarantined will land on HDFS in a subdirectory tree under the folder defined in the [PNDA salt pillar](https://github.com/pndaproject/platform-salt/blob/develop/pillar/pnda.sls). The quarantined data will be further be partitioned based on the topic name and the ingest time.

It should also be noted that only the [10](https://github.com/pndaproject/platform-gobblin-modules/blob/ce0db7bec58804f4bdb4457d325dba6416b9ac2e/src/main/java/gobblin/pnda/PNDAAbstractConverter.java#L68) first events will be quarantined per misconfigured topics. After altering the configuration, the `kafka-consumer-groups.sh` cli (also available on the kafka node in the cluster on the /opt/pnda/kafka/bin/ directory) can be used to reset the conumer group offset for the misconfigured topic. 
