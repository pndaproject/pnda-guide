# Preparing topics

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
