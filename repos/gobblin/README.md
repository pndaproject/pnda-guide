# Gobblin 

Gobblin is a universal data ingestion framework for extracting, transforming, and loading large volume of data from a variety of data sources, e.g. databases, REST APIs, FTP/SFTP servers, filers, etc. onto Hadoop. Gobblin handles the common routine tasks required for all data ingestion ETLs, including job/task scheduling, task partitioning, error handling, state management, data quality checking, data publishing, etc. Gobblin ingests data from different data sources in the same execution framework, and manages metadata of different sources all in one place. This, combined with other features such as auto scalability, fault tolerance, data quality assurance, extensibility, and the ability of handling data model evolution, makes Gobblin an easy-to-use, self-serving, and efficient data ingestion framework.

# [PNDA](http://pnda.io) Fork

This fork adds the following:

* A new converter to convert messages read from Kafka to the PNDA Avro schema (gobblin.pnda.PNDAConverter)
* A new writer writing data with the kitesdk library (gobblin.pnda.PNDAKiteWriterBuilder)

The converter can only work with a Kafka-compatible source. The workflow is:

    KafkaSimpleSource ► PNDAConverter ► [SchemaRowCheckPolicy] ► PNDAKiteWriter

## Build

Gobblin needs to be compiled against CDH hadoop dependencies with the right version:

    $ ./gradlew clean build -PuseHadoop2 -PhadoopVersion=2.6.0-cdh5.4.9

## Configuration

### Kite datasets

Two kite datasets need to be created.

The first one is the main dataset where all the valid PNDA messages are stored. The schema is:

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

The second one is where all data in the wrong format are stored. The schema is:

    {"namespace": "pnda.entity",
     "type"     : "record",
     "name"     : "DeserializationError",
     "fields"   : [
       {"name": "topic",     "type": "string"},
       {"name": "timestamp", "type": "long"},
       {"name": "reason",    "type": ["string", "null"]},
       {"name": "payload",   "type": "bytes"}
     ]
    }

See the [Kite CLI reference](http://kitesdk.org/docs/1.1.0/cli-reference.html) for information on creating a Kite dataset. 

For example, to create the first dataset with the following partition strategy (`partition.json` file):

    [
        {"type": "identity", "source": "src", "name": "source"},
        {"type": "year",     "source": "timestamp"},
        {"type": "month",    "source": "timestamp"},
        {"type": "day",      "source": "timestamp"},
        {"type": "hour",     "source": "timestamp"}
    ]

Then execute the following command:

    $ kite-dataset create --schema pnda.avsc dataset:hdfs://192.168.0.227:8020/tmp/PNDA_datasets/master --partition-by partition.json

### Property file

The `source.schema` property must be set to deserialize AVRO data coming from kafka:

    source.schema={"namespace": "pnda.entity", \
                   "type": "record",                            \
                   "name": "event",                             \
                   "fields": [                                  \
                       {"name": "timestamp", "type": "long"},   \
                       {"name": "src",       "type": "string"}, \
                       {"name": "host_ip",   "type": "string"}, \
                       {"name": "rawdata",   "type": "bytes"}   \
                   ]                                            \
                  }

It can also be a the filename of an AVRO schema.

The `converter.classes` property must include the `gobblin.pnda.PNDAConverter` class.
`PNDA.quarantine.dataset.uri` is optional. If not set, wrong messages will be discarded.

    converter.classes=gobblin.pnda.PNDAConverter
    PNDA.quarantine.dataset.uri=dataset:hdfs://192.168.0.227:8020/tmp/PNDA_datasets/quarantine


The `writer.builder.class` property must be set to `gobblin.pnda.PNDAKiteWriterBuilder` to enable the Kite writer.
`kite.writer.dataset.uri` is mandatory.

    writer.builder.class=gobblin.pnda.PNDAKiteWriterBuilder
    kite.writer.dataset.uri=dataset:hdfs://192.168.0.227:8020/tmp/PNDA_datasets/master

## PNDA Dataset creation

In the context of the PNDA platform, Gobblin is normally run on a regular basis every 30 minutes. Datasets are created dynamically on HDFS by discovering available topics in Kafka.

It's important to note that datasets are not created by topics, but dependending on the `src` field in PNDA Avro messages.

Datasets are stored on HDFS in the `/user/pnda/PNDA_datasets/datasets` directory in the [Kite](http://kitesdk.org/) format, and are partitioned by source and timestamp.

The current partition strategy will write the data read from Kafka to the following hierarchy:

```
source=SSS/year=YYYY/month=MM/day=DD/hour=HH
```

In the path above, `SSS` represents the value of the `src` field in the PNDA Avro schema.

For example, the following message read from Kafka:

    {
      "timestamp": 1462886335,
      "src": "netflow",
      "host_ip": "192.168.0.15",
      "rawdata": "XXXXXxxxxXXXX"
    }
    
will be written to:

```
/user/pnda/PNDA_datasets/datasets/source=netflow/year=2016/month=05/day=10/hour=13/random-uuid.avro
```

## Documentation

See the [Gobblin documentation](https://github.com/linkedin/gobblin/wiki).

## Getting Started

### Building Gobblin

Download or clone the Gobblin repository (say, into `/path/to/gobblin`) and run the following command:

    $ cd /path/to/gobblin
    $ ./gradlew clean build

After Gobblin is successfully built, you will find a tarball named `gobblin-dist.tar.gz` under the project root directory. Copy the tarball out to somewhere and untar it, and you should see a directory named `gobblin-dist`, which initially contains three directories: `bin`, `conf`, and `lib`. Once Gobblin starts running, a new subdirectory `logs` will be created to store logs.

### Building against a Specific Hadoop Version

Gobblin uses the Hadoop core libraries to talk to HDFS as well as to run on Hadoop MapReduce. Because the protocols have changed in different versions of Hadoop, you must build Gobblin against the same version that your cluster runs. By default, Gobblin is built against version 1.2.1 of Hadoop 1, and against version 2.3.0 of Hadoop 2, but you can choose to build Gobblin against a different version of Hadoop.

The build command above will build Gobblin against the default version 1.2.1 of Hadoop 1. To build Gobblin against a different version of Hadoop 1, e.g., 1.2.0, run the following command:

	$ ./gradlew clean build -PhadoopVersion=1.2.0

To build Gobblin against the default version (2.3.0) of Hadoop 2, run the following command:

	$ ./gradlew clean build -PuseHadoop2

To build Gobblin against a different version of Hadoop 2, e.g., 2.2.0, run the following command:

	$ ./gradlew clean build -PuseHadoop2 -PhadoopVersion=2.2.0

For more information on the different build options for Gobblin, check out the [Gobblin Build Options](https://github.com/linkedin/gobblin/wiki/Gobblin-Build-Options) wiki.

### Running Gobblin

Out of the box, Gobblin can run either in standalone mode on a single box or on Hadoop MapReduce. Please refer to the [Gobblin Deployment](https://github.com/linkedin/gobblin/wiki/Gobblin%20Deployment) page in the documentation for an overview of the deployment modes and how to run Gobblin in different modes.

### Running the Examples

Please refer to the [Getting Started](https://github.com/linkedin/gobblin/wiki/Getting%20Started) page in the documentation on how to run the examples.

## Configuration

Please refer to the [Configuration Glossary](https://github.com/linkedin/gobblin/wiki/Configuration%20Properties%20Glossary) page in the documentation for an overview on the configuration properties of Gobblin.
