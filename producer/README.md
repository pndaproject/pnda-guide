# Producer Integration

Kafka is the "front door" of PNDA, allowing the ingest of high-velocity data streams, distributing data to all interested consumers and decoupling data sources from data processing applications and platform clients.

It is normally not necessary to create a new producer to start acquiring network data as there are a growing number of data plugins that have already been integrated with PNDA.  It’s not always clear which plugins to use for which data types, hence we’ve summarised some common combinations in the table at the bottom of this page.

If you have have other data sources you want to integrate with PNDA it’s easy enough to write a PNDA producer – see http://pnda.io/pnda-guide/producer/producer.html

PNDA adopts a [schema-on-read](https://www.techopedia.com/definition/30153/schema-on-read) approach to data processing, so all data directed towards the platform is stored in as close to its raw form as possible. The only requirement is that each datum is encoded as a simple Avro schema fragment that adds the logical & network source of the data and a timestamp to the data payload.

Kafka data is stored in topics, each topic being divided into partitions and each partition being replicated to avoid data loss. Ingest is achieved by delivering data through a "producer" which is implemented to send data to one or more well defined topics by direct connection to the broker cluster. Load balancing is carried out by the broker cluster itself via negotiation with topic partition leaders.

PNDA is typically deployed with a set of well defined topics in accordance with the deployment context, each topic being carefully configured with a set of replicated partitions in line with the expected ingest and consumption rates. By convention topics are named according to a hierarchical scheme such that consumers are able to "whitelist" data of interest and subscribe to multiple topics at once (e.g. `mytelco.service6.netflow.*` or `mytelco.*`).

PNDA includes tools for managing topics, partitions and brokers and for monitoring the data flow across them.

Integrators can make use of the high and low level [Kafka APIs](http://kafka.apache.org/documentation.html#api). Please refer to our [Data Preparation Guide](data-preparation.md) to understand how to encapsulate data to the required Avro schema. We will also provide a reference in a variety of common implementation languages to illustrate how to correctly use the Avro schema in conjunction with the Kafka API.

# Data types mapped to existing PNDA producers

Data Type | Data Aggregator | Data Aggregator Reference | PNDA Producer Reference
--------- | --------------- | ------------------------- | -----------------------
BGP (inc. BGP LS) | OpenBMP | http://www.openbmp.org/#!index.md#Using_Kafka_for_Collector_Integration | http://pnda.io/pnda-guide/producer/openbmp.html
BGP | PMACCT (BGP listener) | http://www.pmacct.net/ | http://pnda.io/pnda-guide/producer/pmacct.html
Bulk Ingest | PNDA Bulk Ingest Tool |  | http://pnda.io/pnda-guide/bulkingest/ 
ISIS | PMACCT (ISIS listener) | http://www.pmacct.net/ | http://pnda.io/pnda-guide/producer/pmacct.html 
Cisco XR streaming telemetry | Pipeline  | https://github.com/cisco/bigmuddy-network-telemetry-collector | 
CollectD (CollectD supports multiple plugins as listed here https://collectd.org/wiki/index.php/Table_of_Plugins) | Logstash | https://www.elastic.co/guide/en/logstash/current/plugins-codecs-collectd.html | http://pnda.io/pnda-guide/repos/prod-logstash-codec-avro/ 
IoT sensor via HTTP | Node-RED  | https://nodered.org | 
Logstash (Logstash supports multiple plugins as listed here https://www.elastic.co/guide/en/logstash/current/input-plugins.html) | Logstash |  | http://pnda.io/pnda-guide/repos/prod-logstash-codec-avro/ 
NETCONF Notifications | ODL | http://www.opendaylight.org/ | http://pnda.io/pnda-guide/producer/opendl.html 
Netflow / IPFIX | Logstash | https://www.elastic.co/guide/en/logstash/current/plugins-codecs-netflow.html  | http://pnda.io/pnda-guide/repos/prod-logstash-codec-avro/  
Netflow / IPFIX / sFlow | pmacct | http://www.pmacct.net/ | http://pnda.io/pnda-guide/producer/pmacct.html 
Openstack | Work in progress |  | 
sFlow | Logstash | https://github.com/ashangit/logstash-codec-sflow | http://pnda.io/pnda-guide/repos/prod-logstash-codec-avro/
SNMP Metrics and Traps | ODL | https://wiki.opendaylight.org/view/SNMP_Plugin:Getting_Started | http://pnda.io/pnda-guide/producer/opendl.html
SNMP Traps  | Logstash | https://www.elastic.co/guide/en/logstash/current/plugins-inputs-snmptrap.html | http://pnda.io/pnda-guide/repos/prod-logstash-codec-avro/
Syslog | Logstash | https://www.elastic.co/guide/en/logstash/current/plugins-inputs-syslog.html | http://pnda.io/pnda-guide/repos/prod-logstash-codec-avro/
Syslog (RFC3164 or RFC5424 - needed for newer IOS/IOS XR/ NX OS etc.)  | Logstash  | https://gist.github.com/donaldh/89b7304981f96497c94fe4d98bb03d71 | http://pnda.io/pnda-guide/repos/prod-logstash-codec-avro/ 
 
