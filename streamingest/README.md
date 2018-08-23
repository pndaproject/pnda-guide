# Producer Integration

Kafka is the "front door" of PNDA, allowing the ingest of high-velocity data streams, distributing data to all interested consumers and decoupling data sources from data processing applications and platform clients.

It is normally not necessary to create a new producer to start acquiring network data as any data is supported out of the box. Additionally, some encodings do benefit from special accommodations. But even for these, no new producer should be required as there are a growing number of data plugins that have already been integrated with PNDA.  It’s not always clear which plugins to use for which data types, hence we’ve summarized some common combinations in the table at the bottom of this page.

If you do have other data sources you want to integrate with PNDA it’s easy enough to write a PNDA producer – see [producer.md](producer.md)

PNDA adopts a [schema-on-read](https://www.techopedia.com/definition/30153/schema-on-read) approach to data processing, so all data directed towards the platform is stored in as close to its raw form as possible. When data is persisted, each datum is ensured compliance to a consistent Avro wrapper that contains both the logical source of the data and a timestamp besides the data payload.

Kafka data is stored in topics, each topic being divided into partitions and each partition being replicated to avoid data loss. Ingest is achieved by delivering data through a "producer" which is implemented to send data to one or more well defined topics by direct connection to the broker cluster. Load balancing is carried out by the broker cluster itself via negotiation with topic partition leaders.

PNDA is typically deployed with a set of well defined topics in accordance with the deployment context, each topic being carefully configured with a set of replicated partitions in line with the expected ingest and consumption rates. Please refer to our [Topic Preparation Guide](topic-preparation.md) to understand how to create and setup up topics. By convention topics are named according to a hierarchical scheme such that consumers are able to "whitelist" data of interest and subscribe to multiple topics at once (e.g. `mytelco.service6.netflow.*` or `mytelco.*`).

PNDA includes tools for managing topics, partitions and brokers and for monitoring the data flow across them.

Integrators can make use of the high and low level [Kafka APIs](http://kafka.apache.org/documentation.html#api). Please refer to our [Topic Preparation Guide](topic-preparation.md) to discover how to leverage advanced feature that come with some dedicated encodings and our [Data Preparation Guide](data-preparation.md) to understand how to encapsulate data for those encoding options.

# Data types mapped to existing PNDA producers

Data Type | Data Aggregator | Data Aggregator Reference | PNDA Producer Reference
--------- | --------------- | ------------------------- | -----------------------
BGP (inc. BGP LS) | OpenBMP | http://www.openbmp.org/#!index.md#Using_Kafka_for_Collector_Integration | [openbmp](openbmp.md)
BGP | PMACCT (BGP listener) | http://www.pmacct.net/ | [pmacct](pmacct.md)
Bulk Ingest | PNDA Bulk Ingest Tool |  | http://pnda.io/pnda-guide/bulkingest/ 
ISIS | PMACCT (ISIS listener) | http://www.pmacct.net/ | [pmacct](pmacct.md)
Cisco XR streaming telemetry | Pipeline  | https://github.com/cisco/bigmuddy-network-telemetry-collector | 
CollectD (CollectD supports multiple plugins as listed here https://collectd.org/wiki/index.php/Table_of_Plugins) | Logstash | https://www.elastic.co/guide/en/logstash/current/plugins-codecs-collectd.html | [logstash](logstash.md)
IoT sensor via HTTP | Node-RED  | https://nodered.org | 
Logstash (Logstash supports multiple plugins as listed here https://www.elastic.co/guide/en/logstash/current/input-plugins.html) | Logstash |  | [logstash](logstash.md)
NETCONF Notifications | ODL | http://www.opendaylight.org/ | [opendl](opendl.md)
Netflow / IPFIX | Logstash | https://www.elastic.co/guide/en/logstash/current/plugins-codecs-netflow.html  | [logstash](logstash.md)
Netflow / IPFIX / sFlow | pmacct | http://www.pmacct.net/ | [pmacct](pmacct.md)
Openstack | Work in progress |  | 
sFlow | Logstash | https://github.com/ashangit/logstash-codec-sflow | [logstash](logstash.md)
SNMP Metrics and Traps | ODL | https://wiki.opendaylight.org/view/SNMP_Plugin:Getting_Started | [opendl](opendl.md)
SNMP Traps  | Logstash | https://www.elastic.co/guide/en/logstash/current/plugins-inputs-snmptrap.html | [logstash](logstash.md)
Syslog | Logstash | https://www.elastic.co/guide/en/logstash/current/plugins-inputs-syslog.html | [logstash](logstash.md)
Syslog (RFC3164 or RFC5424 - needed for newer IOS/IOS XR/ NX OS etc.)  | Logstash  | https://gist.github.com/donaldh/89b7304981f96497c94fe4d98bb03d71 | [logstash](logstash.md)

