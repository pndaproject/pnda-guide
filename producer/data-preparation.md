# Preparing data

Ingested data should be encapsulated in an Avro schema and published on a pre-defined Kafka topic or set of topics.

## Avro encapsulation

The Avro schema is:

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

Explanation of the fields:

* `timestamp`: Timestamp of when the event was generated/ingested by PNDA, **in milliseconds**.
* `src`: source of the event. Where does the event come from ? (e.g. syslog, collectd) it should be a high level abstracted source
* `host_ip`: source IP address of the machine where event is generated/ingested by PNDA
* `rawdata`: The 'raw' data goes here, it's an array of **bytes** and is not interpreted by PNDA in any way.

Events should be encoded as **Avro fragments**. This means that the schema is *not* included in each event but is instead made available at encode/decode time.

Please refer to the [Avro documentation](https://avro.apache.org/docs/1.7.7/gettingstartedjava.html) for more information about encoding and decoding.

## Verification

Before integrating data with PNDA, check that it is encoded correctly using the [Avro tools](http://www.eu.apache.org/dist/avro/stable/java/).

### Example

If the data has been encoded correctly, then the Avro tools will be able to decode it without any errors. In this case, `output.avro` contains some netflow data and our schema is in a file named `schema.avsc`.

	$ hexdump -C output.avro | head
	00000000  e0 d8 8a ce 96 54 0e 6e  65 74 66 6c 6f 77 18 31  |.....T.netflow.1|
	00000010  37 32 2e 33 30 2e 31 39  34 2e 37 c4 08 7b 22 76  |72.30.194.7..{"v|
	00000020  65 72 73 69 6f 6e 22 3a  22 35 22 2c 22 66 6c 6f  |ersion":"5","flo|
	00000030  77 5f 73 65 71 5f 6e 75  6d 22 3a 22 31 36 30 33  |w_seq_num":"1603|
	00000040  36 35 38 35 35 30 22 2c  22 65 6e 67 69 6e 65 5f  |658550","engine_|
	00000050  74 79 70 65 22 3a 22 30  22 2c 22 65 6e 67 69 6e  |type":"0","engin|
	00000060  65 5f 69 64 22 3a 22 30  22 2c 22 73 61 6d 70 6c  |e_id":"0","sampl|
	00000070  69 6e 67 5f 61 6c 67 6f  72 69 74 68 6d 22 3a 22  |ing_algorithm":"|
	00000080  30 22 2c 22 73 61 6d 70  6c 69 6e 67 5f 69 6e 74  |0","sampling_int|
	00000090  65 72 76 61 6c 22 3a 22  30 22 2c 22 66 6c 6f 77  |erval":"0","flow|
	000000a0  5f 72 65 63 6f 72 64 73  22 3a 22 33 30 22 2c 22  |_records":"30","|
	000000b0  69 70 76 34 5f 73 72 63  5f 61 64 64 72 22 3a 22  |ipv4_src_addr":"|
	000000c0  31 32 38 2e 31 30 37 2e  31 38 33 2e 31 38 36 22  |128.107.183.186"|
	000000d0  2c 22 69 70 76 34 5f 64  73 74 5f 61 64 64 72 22  |,"ipv4_dst_addr"|
	000000e0  3a 22 31 37 32 2e 33 30  2e 31 35 39 2e 31 35 37  |:"172.30.159.157|
	000000f0  22 2c 22 69 70 76 34 5f  6e 65 78 74 5f 68 6f 70  |","ipv4_next_hop|
	00000100  22 3a 22 31 37 32 2e 33  30 2e 31 35 32 2e 31 30  |":"172.30.152.10|
	00000110  22 2c 22 69 6e 70 75 74  5f 73 6e 6d 70 22 3a 22  |","input_snmp":"|
	00000120  32 22 2c 22 6f 75 74 70  75 74 5f 73 6e 6d 70 22  |2","output_snmp"|
	00000130  3a 22 32 38 22 2c 22 69  6e 5f 70 6b 74 73 22 3a  |:"28","in_pkts":|
	00000140  22 36 22 2c 22 69 6e 5f  62 79 74 65 73 22 3a 22  |"6","in_bytes":"|
    
	$ cat schema.avsc
	{"namespace": "pnda.entity",
	"type": "record",
	"name": "event",
	"fields": [
	     {"name": "timestamp", "type": "long"},
	     {"name": "src",       "type": "string"},
	     {"name": "host_ip",   "type": "string"},
	     {"name": "rawdata",   "type": "bytes"}
	]
	}
	
	$ java -jar avro-tools-1.7.7.jar fragtojson --schema-file schema.avsc output.avro
	{
	  "timestamp" : 1446143678000,
	  "src" : "netflow",
	  "host_ip" : "172.30.194.7",
	  "rawdata" : "{\"version\":\"5\",\"flow_seq_num\":\"1603658550\",\"engine_type\":\"0\",\"engine_id\":\"0\",\"sampling_algorithm\":\"0\",\"sampling_interval\":\"0\",\"flow_records\":\"30\",\"ipv4_src_addr\":\"128.107.183.186\",\"ipv4_dst_addr\":\"172.30.159.157\",\"ipv4_next_hop\":\"172.30.152.10\",\"input_snmp\":\"2\",\"output_snmp\":\"28\",\"in_pkts\":\"6\",\"in_bytes\":\"291\",\"first_switched\":\"2015-10-29T18:34:34.632Z\",\"last_switched\":\"2015-10-29T18:34:36.633Z\",\"l4_src_port\":\"51315\",\"l4_dst_port\":\"51000\",\"tcp_flags\":\"27\",\"protocol\":\"6\",\"src_tos\":\"0\",\"src_as\":\"0\",\"dst_as\":\"0\",\"src_mask\":\"0\",\"dst_mask\":\"25\"}"
	}


### Topic hierarchy

You can define your own topic naming scheme. For example, you can have one topic per input source.
We recommend using a topic hierarchy such as the following:

     {format}.{aggregator}.{source}
     {format}: avro
     {aggregator}: log, metric etc...
     {source}: unique string identifying a source. Example: isr, csr, openstack

A topic name is a unique string identifying a log queue into Kafka for pushing / pulling messages. In addition to describing the topic succinctly, a hierarchical naming scheme lets you conveniently filter topics when subscribing. For example, a consumer can subscribe to `avro.log.*` to get all messages from `avro.log.isr`, `avro.log.csr` and `avro.log.openstack`.

## Data management

By default, Kafka keeps data for 24 hours for each topic. You can use the Kafka Manager to override the default data retention policy on a time or size basis.

## Extend the Avro schema

If you want to extend the current PNDA Avro schema, without breaking the platform, this is possible but you need to follow these rules:

* keep the base pnda.entity fields (timestamp, src, host_ip and rawdata)
* and the extra fields after rawdata definition

Here is an example of a new schema:

	{"namespace": "pnda.entity",
	"type": "record",
	"name": "event",
	"fields": [
	     {"name": "timestamp", "type": "long"},
	     {"name": "src",       "type": "string"},
	     {"name": "host_ip",   "type": "string"},
	     {"name": "rawdata",   "type": "bytes"},
	     {"name": "name",   "type": "string"},
	     {"name": "uid",   "type": "long"},
	     {"name": "desc",   "type": "string"},
	]
	}

By doing this, you will ensure that this will not break any components of the platform. But keep in mind that this new fields will not be stored within HDFS as Gobblin only managed the original format. There will be no impact on other components, you will be able to use these new fields on Kafka producer/consumer side which then include the Spark Streaming application.

### Producer example

Based on the previous schema update, here is the updates you need to make on the [kafka clients example repo](https://github.com/pndaproject/example-kafka-clients) in Python:

	schema_path = "./dataplatform-raw-v2.avsc"

	writer.write({"timestamp": CURRENT_TIME_MILLIS(),
                  "src": "collecd",
                  "host_ip": "bb80:0:1:2:a00:bbff:bbee:b123",
                  "rawdata": collectd_alea,
	     		  "name":"us-123",
	     		  "uid": 123456789,
	     		  "desc":"US 123 in SJC"}, encoder)

### Consumer example

On the consumer side, you just need to update the schema, here is a sample output from the Python consumer code:

	ConsumerRecord(topic=u'avro.log.localtest', partition=0, offset=65, timestamp=-1, timestamp_type=0, key=None, value="\xaa\x8e\xff\xa5\xbdW\x0ccollectd:bb80:0:1:2:a00:bbff:bbee:b123\xc4\x01{'host':'pnda5','collectd_type':'memory','value':'1779803','timestamp':'2017-08-16T09:28:59.000Z'}\x0cus-123\xaa\xb4\xdeu\x1aUS 123 in SJC\x02", checksum=1582345509, serialized_key_size=-1, serialized_value_size=169)
	{uu'src': u'collectd', u'uid': 123456789, u'timestamp': 1502875739029, u'host_ip': u'bb80:0:1:2:a00:bbff:bbee:b123', u'name': u'us-123', u'rawdata': "{'host':'pnda5','collectd_type':'memory','value':'1779803','timestamp':'2017-08-16T09:28:59.000Z'}", u'desc': u'US 123 in SJC'}

### Dataset in HDFS

If we now look what is stored in HDFS through Gobblin, use the Hue UI in order to browse and download the avro file and then run:

	$ java -jar avro-tools-1.7.7.jar tojson cff419b3-6c2a-4e9b-b2b3-a9b4d52d230c.avro
	{"timestamp":1502875281109,"src":"collectd","host_ip":"bb80:0:1:2:a00:bbff:bbee:b123","rawdata":"{'host':'pnda5','collectd_type':'cpu','value':'17','timestamp':'2017-08-16T09:21:21.000Z'}"}
	{"timestamp":1502875282112,"src":"collectd","host_ip":"bb80:0:1:2:a00:bbff:bbee:b123","rawdata":"{'host':'pnda5','collectd_type':'memory','value':'3593985','timestamp':'2017-08-16T09:21:22.000Z'}"}

As you can see, you only have the original field from pnda.entity version 1 stored in HDFS
