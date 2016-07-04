# Producer Integration

Kafka is the "front door" of PNDA, allowing the ingest of high-velocity data streams, distributing data to all interested consumers and decoupling data sources from data processing applications and platform clients.

PNDA adopts a [schema-on-read](https://www.techopedia.com/definition/30153/schema-on-read) approach to data processing, so all data directed towards the platform is stored in as close to its raw form as possible. The only requirement is that each datum is encoded as a simple Avro schema fragment that adds the logical & network source of the data and a timestamp to the data payload.

Kafka data is stored in topics, each topic being divided into partitions and each partition being replicated to avoid data loss. Ingest is achieved by delivering data through a "producer" which is implemented to send data to one or more well defined topics by direct connection to the broker cluster. Load balancing is carried out by the broker cluster itself via negotiation with topic partition leaders.

PNDA is typically deployed with a set of well defined topics in accordance with the deployment context, each topic being carefully configured with a set of replicated partitions in line with the expected ingest and consumption rates. By convention topics are named according to a hierarchical scheme such that consumers are able to "whitelist" data of interest and subscribe to multiple topics at once (e.g. `mytelco.service6.netflow.*` or `mytelco.*`).

PNDA includes tools for managing topics, partitions and brokers and for monitoring the data flow across them.

Integrators can make use of the high and low level [Kafka APIs](http://kafka.apache.org/documentation.html#api). Please refer to our [Data Preparation Guide](data-preparation.md) to understand how to encapsulate data to the required Avro schema. We will also provide a reference in a variety of common implementation languages to illustrate how to correctly use the Avro schema in conjunction with the Kafka API.

However, it is normally not necessary to create a new producer to start acquiring network data—there are a number of existing technologies that can already deliver data to PNDA.
