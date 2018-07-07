# Preparing data

PNDA supports any data type and it is normally not necessary to prepare data for ingestion by the platform. Yet, as discussed in our [Topic Preparation Guide](topic-preparation.md), data that adheres to the same schema as used by the platform can benefit from special treatment. To enable this, the ingested data should be encapsulated in an Avro schema and published on a pre-defined Kafka topic or set of topics specially configured to recognize these matching schema's.

## Avro encapsulation

The Avro schema is:

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

Explanation of the fields:

* `timestamp`: Timestamp of when the event was generated/ingested by PNDA, **in milliseconds**.
* `source`: source of the event. Where does the event come from ? (e.g. syslog, collectd) it should be a high level abstracted source
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
	     {"name": "source",    "type": "string"},
	     {"name": "rawdata",   "type": "bytes"}
	]
	}
	
	$ java -jar avro-tools-1.7.7.jar fragtojson --schema-file schema.avsc output.avro
	{
	  "timestamp" : 1446143678000,
	  "source" : "netflow",
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

## Avro schema evolution


### Simple schema update 

If you want to extend the current PNDA Avro schema this is possible but follow these rules is essential:

* Keep the base pnda.entity fields (timestamp, source and rawdata)
* Add the extra fields after rawdata definition
* Only use the Avro schema for 'envelope' data - non-domain-specific per-deployment metadata that can be truly populated for *all* data. If you're considering more than one variation of the Avro schema, probably it should be passed in rawdata instead

Here is an example of a new schema:

	{"namespace": "pnda.entity",
	"type": "record",
	"name": "event",
	"fields": [
	     {"name": "timestamp", "type": "long"},
	     {"name": "source",    "type": "string"},
	     {"name": "rawdata",   "type": "bytes"},
	     {"name": "name",      "type": "string"},
	     {"name": "uid",       "type": "long"},
	     {"name": "desc",      "type": "string"},
	]
	}

By doing this, you will ensure compatibilty with the platform now and in the future. Keep in mind that any new fields will not be stored in HDFS by Gobblin. You will be able to use these new fields in Kafka producers/consumers, for example Spark Streaming applications.

#### Producer example

Based on the previous extended schema example, here are the updates you would need to make to the [kafka clients example repo](https://github.com/pndaproject/example-kafka-clients) in Python:

	schema_path = "./dataplatform-raw-v2.avsc"

	writer.write({"timestamp": CURRENT_TIME_MILLIS(),
                  "source": "collecd",
                  "rawdata": collectd_alea,
	     		        "name":"us-123",
	     		        "uid": 123456789,
	     		        "desc":"US 123 in SJC"}, encoder)

#### Consumer example

On the consumer side, you just need to update the schema, here is a sample output from the Python consumer code:

	ConsumerRecord(topic=u'avro.log.localtest', partition=0, offset=65, timestamp=-1, timestamp_type=0, key=None, value="\xaa\x8e\xff\xa5\xbdW\x0ccollectd:bb80:0:1:2:a00:bbff:bbee:b123\xc4\x01{'host':'pnda5','collectd_type':'memory','value':'1779803','timestamp':'2017-08-16T09:28:59.000Z'}\x0cus-123\xaa\xb4\xdeu\x1aUS 123 in SJC\x02", checksum=1582345509, serialized_key_size=-1, serialized_value_size=169)
	{uu'source': u'collectd', u'uid': 123456789, u'timestamp': 1502875739029, u'name': u'us-123', u'rawdata': "{'host':'pnda5','collectd_type':'memory','value':'1779803','timestamp':'2017-08-16T09:28:59.000Z'}", u'desc': u'US 123 in SJC'}

#### Dataset in HDFS

If we now look what is stored in HDFS through Gobblin, use the Hue UI in order to browse and download the avro file and then run:

	$ java -jar avro-tools-1.7.7.jar tojson cff419b3-6c2a-4e9b-b2b3-a9b4d52d230c.avro
	{"timestamp":1502875281109,"source":"collectd","rawdata":"{'host':'pnda5','collectd_type':'cpu','value':'17','timestamp':'2017-08-16T09:21:21.000Z'}"}
	{"timestamp":1502875282112,"source":"collectd","rawdata":"{'host':'pnda5','collectd_type':'memory','value':'3593985','timestamp':'2017-08-16T09:21:22.000Z'}"}

As you can see, you only have the original field from pnda.entity version 1 stored in HDFS

### Avro Schema evolution 

If you want to manage evolution of the current Avro schema without breaking the platform but also ensure you are not breaking the platform and these evolution are taking into account within the whole platform, you will need to do the same as above put upgrade Gobblin configuration and also change the kite dataset configuration.
Let's take an example and all the steps required to well perform and manage an avro schema evolution.

#### Schema upgrade and versioning

As an example, let's imagine we want to be able to add 3 more fields to the Avro schema that are optional. As above, add those extra fields after the rawdata and use [Avro Union Type](https://avro.apache.org/docs/1.8.1/spec.html#Unions) which allows for optional fields with a default value. Also update the namespace in order to include the versioning. Here is the newer version of the schema:

	{
		"namespace": "pnda.entity.v2",
	 	"type": "record",
	 	"name": "event",
	 	"fields": [
		     {"name": "timestamp",   "type": "long"},
		     {"name": "source",      "type": "string"},
		     {"name": "rawdata",     "type": "bytes"},
		     {"name": "of_1", "type": ["null", "string"]},
		     {"name": "of_2", "type": ["null", "long"]},
		     {"name": "of_3", "type": ["null", "string"]}
	 ]
	}

As you can see, we've added 3 more fields, 2 strings and 1 long type named of_1, of_2 and of_3 respectively. Note also that we updated the namespace to include a version identifier to differentiate from the original pnda.entity.

#### Producer example

Based on the above schema, here are the updates you need to make to [kafka clients example repo](https://github.com/pndaproject/example-kafka-clients) in Python:

	schema_path = "./dataplatform-raw-v2.avsc"

	writer.write({"timestamp": CURRENT_TIME_MILLIS(),
                  "source": "collectd",
                  "rawdata": collectd_alea,
	     		  "of_1":"us-123",
	     		  "of_2": 123456789,
	     		  "of_3":"US 123 in SJC"}, encoder)

	writer.write({"timestamp": CURRENT_TIME_MILLIS(),
                  "source": "collectd",
                  "rawdata": collectd_alea,
	     		  "of_3":"FR 124 in Paris"}, encoder)

	writer.write({"timestamp": CURRENT_TIME_MILLIS(),
                  "source": "collectd",
                  "rawdata": collectd_alea,
	     		  "of_1":"uk-125"}, encoder)

As you can see, we've pushed 3 messages, the first of which specified all possible fields while the next 2 lack of_1 & of_2 or of_2 & of_3.

#### Gobblin

If you wish the new fields added to the schema to be persisted to HDFS then changes are required to Gobblin configuration.

* Gobblin configuration file [mr.pull.tpl](https://github.com/pndaproject/platform-salt/blob/develop/salt/gobblin/templates/mr.pull.tpl) requires a schema update:

source.schema={"namespace": "pnda.entity.v2",                 \
               "type": "record",                            \
               "name": "event",                             \
               "fields": [                                  \
                   {"name": "timestamp", "type": "long"},   \
                   {"name": "source",    "type": "string"}, \
                   {"name": "rawdata",   "type": "bytes"},   \
                   {"name": "of_1",      "type": ["null", "string"]}, \
                   {"name": "of_2", 	 "type": ["null", "long"]},   \
                   {"name": "of_3",      "type": ["null", "string"]} \
               ]                                            \
              }

If you already have a running cluster, you will need to update the edge node which contains the gobblin role that installed Gobblin:

* Gobblin: update the mr.pull.tpl file in /opt/pnda/gobblin/configs/mr.pull

### HDFS data

Now, having ingested some data and having downloaded an Avro file stored in HDFS by Gobblin:

* Checking the schema from the schema from the avro file:

	java -jar avro-tools-1.7.7.jar getschema 3cfdf9d4-bd05-4456-aadd-df3a0e522ff8.avro 
	{
	  "type" : "record",
	  "name" : "event",
	  "namespace" : "pnda.entity.v2",
	  "fields" : [ {
	    "name" : "timestamp",
	    "type" : "long"
	  }, {
	    "name" : "source",
	    "type" : "string"
	  }, {
	    "name" : "rawdata",
	    "type" : "bytes"
	  }, {
	    "name" : "of_1",
	    "type" : [ "null", "string" ]
	  }, {
	    "name" : "of_2",
	    "type" : [ "null", "long" ]
	  }, {
	    "name" : "of_3",
	    "type" : [ "null", "string" ]
	  } ]
	}

* Check the messages:

	java -jar avro-tools-1.7.7.jar tojson 3cfdf9d4-bd05-4456-aadd-df3a0e522ff8.avro 
	{"timestamp":1503417768521,"source":"collectd","rawdata":"{'host':'pnda123','collectd_type':'cpu','value':'13','timestamp':'2017-08-22T16:02:48.000Z'}","of_1":{"string":"us-123"},"of_2":{"long":123456789},"of_3":{"string":"US 123 in SJC"}}
	{"timestamp":1503417769523,"source":"collectd","rawdata":"{'host':'pdna124','collectd_type':'memory','value':'2820604','timestamp':'2017-08-22T16:02:49.000Z'}","of_1":null,"of_2":null,"of_3":{"string":"FR 124 in Paris"}}
	{"timestamp":1503417770525,"source":"collectd","rawdata":"{'host':'pnda125','collectd_type':'cpu','value':'30','timestamp':'2017-08-22T16:02:50.000Z'}","of_1":{"string":"uk-125"},"of_2":null,"of_3":null}
