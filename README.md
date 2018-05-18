# PNDA Guide

PNDA is a simple, scalable, open big data platform supporting operational and business intelligence analysis for networks and services. This guide provides an overview of PNDA, and will tell you how to set up and use PNDA in your own environment. 

## Quick Links

- [GitHub](https://github.com/pndaproject)
- [JIRA](https://issues.pnda.io)
- [Confluence](https://cwiki.pnda.io)
- [Website](http://pnda.io)
- [Guide](http://pnda.io/guide)
- [Twitter](https://twitter.com/pndaproject)

## [Overview](overview/README.md)

This chapter covers the main components of PNDA, including:

- Data ingress using Logstash, Open Daylight & the bulk ingest tool
- Data distribution with Kafka & Zookeeper
- High velocity stream processing with Spark Streaming
- High volume batch processing with Spark
- Free form data exploration with Jupyter
- Structured query over big data with Impala
- Handling time series with OpenTSDB & Grafana

## [Getting Started](gettingstarted/README.md)

This checklist will get you started setting up a fully operational PNDA cluster, with data flowing in and out.

## [Creating PNDA](provisioning/README.md)

This chapter describes how to provision a PNDA cluster, and includes some background information on SaltStack, OpenStack Heat and AWS CloudFormation.

 * [Creating PNDA](provisioning/OVERVIEW.md)
 
### Background information

 * [Getting started with Heat](provisioning/heat.md)
 * [Getting started with AWS](provisioning/aws.md)
 * [Getting started with SaltStack](provisioning/saltstack.md)
 
### For a complete list of all technologies brought together by PNDA
 
 * [Technology versions](provisioning/versions.md)

## [Console](console/README.md)

The PNDA console provides a real-time overview of all the components in a PNDA cluster. The home page shows health statistics for each component, color-coded by status. Components are grouped into categories, including data distribution, data processing, data storage, applications, etc.

Other pages on the console let you view detailed metrics, deploy packages, run applications, and set data retention policies. 

 * [Metrics](console/metrics.md)
 * [Packages](console/packages.md)
 * [Applications](console/applications.md)
 * [Datasets](console/datasets.md)
 * [UI links and credentials](console/uicredentials.md) 

## [Endpoint Management and Service discovery](em_sd/README.md)

Prior to the 4.0 PNDA release a mixture of fixed IP addresses and individual hostnames were used to wire everything together, with SaltStack inserting the right values into specific config files when PNDA was created. We were also relying on /etc/hosts for all DNS resolution which was not flexible or easy to maintain when adding or removing hosts. 

In addition to this, it was not possible to discover service/endpoints externally without knowing in advance the PNDA deployment scheme and which hosts to address.

To solve all these problems we decided to use [Consul.io](https://www.consul.io/) for endpoints management and service discovery. See the following parts for getting more details on the current implementation using Consul:

 * [Consul Overview](em_sd/consul.md)
 * [DNS in PNDA](em_sd/dns.md)
 * [Service discovery](em_sd/sd.md)

## [Streaming Ingest](streamingest/README.md)

Kafka is the "front door" of PNDA. It handles ingest of data streams from network sources and distributes data to all interested consumers. This chapter covers how to setup PNDA topics and how to integrate and develop "producers", which feed data into the Kafka topics.

 * [Preparing topics](streamingest/topic-preparation.md)
 * [Preparing data](streamingest/data-preparation.md)
 * [Integrating Logstash](streamingest/logstash.md)
 * [Integrating OpenDaylight](streamingest/opendl.md)
 * [Integrating OpenBMP](streamingest/openbmp.md)
 * [Integrating Pmacct](streamingest/pmacct.md)
 * [Developing a custom producer](streamingest/producer.md)

## [Bulk Ingest](bulkingest/README.md)

In addition to streaming ingest via Kafka producers, PNDA also provides an offline bulk ingest tool for those who would like to migrate pre-existing data into the PNDA platform. 

 * [Bulk-ingest tool](https://github.com/pndaproject/platform-tools/tree/master/bulkingest)

## [Consumers](consumer/README.md)

Kafka has a simple, clean design that moves complexity traditionally found inside message brokers into its producers and consumers. A Kafka consumer pulls messages from one or more topics using Zookeeper for discovery, issuing fetch requests to the  brokers leading the partitions it wants to consume. Rather than the broker maintaining state and controlling the flow of data, each consumer controls the rate at which it consumes messages.

## [Packages & Applications](applications/README.md)

Packages are independently deployable units of application layer functionality, and applications are instances of packages. You can use the PNDA console to deploy packages and manage the application lifecycle. The Deployment Manager documentation explains the structure of packages, and the REST API used to deploy them. 

 * [Deployment Manager](https://github.com/pndaproject/platform-deployment-manager)
 * [Example Applications](applications/examples.md)
 * [Spark Streaming and HBase tutorial](applications/ksh.md)
 * [Spark Streaming and OpenTSDB tutorial](applications/kso.md)

## [Log Aggregation](log-aggregation/README.md)

Logs from the various component services that make up PNDA, and the applications that run on PNDA, are collected and stored on the logserver node. 

## [Structured Query](query/README.md)

Apache Impala is a parallel execution engine for SQL queries. It supports low-latency access and interactive exploration of data in HDFS and HBase. Impala allows data to be stored in a raw form, with aggregation performed at query time without requiring upfront aggregation of data.

* [Impala](query/impala.md)

## [Data Exploration](exploration/README.md)

The [Jupyter Notebook](http://jupyter.org) is a web application that allows you to create and share documents that contain live code, equations, visualizations and explanatory text. In PNDA, it supports exploration and presentation of data from HDFS and HBase.

* [Using Jupyter](exploration/jupyter.md)
* [Exploratory data analytics tutorial](exploration/lab.md)
* [Managing application dependencies](exploration/dependencies.md)
* [Using dependencies in your streaming and batch applications](exploration/dep-usage.md)

## [Time Series](timeseries/README.md)

OpenTSDB is a scalable time series database that lets you store and serve massive amounts of time series data, without losing granularity. Grafana is a graph and dashboard builder for visualizing time series metrics.

* [OpenTSDB](timeseries/opentsdb.md)
* [Grafana](timeseries/grafana.md)

## [Security](security/README.md)

A big data infrastructure like PNDA involves a multitude of technologies and tools, and may be deployed in a multi-tenant environment. Providing enterprise grade security for such system is not only complex, but is of primary concern for any production deployment. If you are implementing a client for a PNDA interface or developing a PNDA application, this chapter will cover some security guidelines that you should adhere to when working with individual components. 

## [Resource Management](resourcemanagement/README.md)

Hadoop distributions come with a resource management system. The ResourceManager has two main components: Scheduler and ApplicationsManager. Traditionally organization have a separate set of compute resources for development workloads and for productions workloads. This not only leads to poor average resource utilization and overhead of managing multiple independent clusters but more importantly to duplication of the data, which represent a considerable cost in a big data platform. Consequently, sharing data lakes between these two activities represent considerable cost-savings in infrastructure resources. However, sharing computes resources for production activities with development activities should be done in all respect of the critical SLA's production workloads have the abide by. In a default PNDA deployment, the yarn schedulers have been configured to prioritize the system functionality first (in order not to lose any data), then the production workload and finally as last priority the development applications if any resources are still available. Unfortunately, the Yarn schedulers and especially their queue placement tools are more designed around sharing resources across organizations rather than for a priority based queueing system. For this reason, PNDA has chosen to complement the queue placement policies with its own tool and configuration options.     

## [Repositories](repos/README.md)

The PNDA distribution consists of the following source code repositories and sub-projects:

### Provisioning

 * [platform-salt](http://github.com/pndaproject/platform-salt): provisioning logic for creating PNDA
 * [pnda-cli](http://github.com/pndaproject/pnda-cli): orchestration application for creating PNDA on AWS, OpenStack or an existing pre-prepared cluster 
 * [pnda-dib-elements](http://github.com/pndaproject/pnda-dib-elements): tools for building disk image templates
 * [pnda](https://github.com/pndaproject/pnda): pnda release notes and build system

### Platform

 * [platform-libraries](http://github.com/pndaproject/platform-libraries): libraries for working with interactive notebooks
 * [platform-tools](http://github.com/pndaproject/platform-tools): tools for operating a cluster
     * [bulkingest](http://github.com/pndaproject/platform-tools/tree/master/bulkingest): tools for performing a bulk ingest of data
 * [platform-console-frontend](http://github.com/pndaproject/platform-console-frontend): “single pane of glass” giving operational overview and access to application and data management functions
 * [platform-console-backend](http://github.com/pndaproject/platform-console-backend): APIs that provide data to the console frontend
   * [console-backend-data-logger](http://github.com/pndaproject/platform-console-backend/tree/master/console-backend-data-logger): APIs to ingest data
   * [console-backend-data-manager](http://github.com/pndaproject/platform-console-backend/tree/master/console-backend-data-manager): APIs to provide data
 * [platform-testing](http://github.com/pndaproject/platform-testing): modules that test both the end to end platform and individual components and collect metrics
 * [platform-deployment-manager](http://github.com/pndaproject/platform-deployment-manager): API to manage packages and application deployment and lifecycle
 * [platform-data-mgmnt](http://github.com/pndaproject/platform-data-mgmnt): tools to manage data retention
   * [data-service](http://github.com/pndaproject/platform-data-mgmnt/tree/master/data-service): API to set data retention policies
   * [hdfs-cleaner](http://github.com/pndaproject/platform-data-mgmnt/tree/master/hdfs-cleaner): cron job to clean up HDFS data
   * [oozie-templates](http://github.com/pndaproject/platform-data-mgmnt/tree/master/oozie-templates): templates that archive or delete data
 * [platform-package-repository](http://github.com/pndaproject/platform-package-repository): manages a simple package repository backed by OpenStack Swift
 * [gobblin](http://github.com/pndaproject/gobblin): customized fork of the Gobblin data ingest framework

### Producers

 * [prod-odl-kafka](https://github.com/pndaproject/prod-odl-kafka): plugin to ingest data from OpenDaylight
 * [logstash-codec-pnda-avro](https://github.com/pndaproject/logstash-codec-pnda-avro): patched AVRO codec ingest data from Logstash

### Examples

 * [example-applications](https://github.com/pndaproject/example-applications): example applications that can be built and run on PNDA
   * [spark-batch](https://github.com/pndaproject/example-applications/tree/master/spark-batch): example batch data processing application
   * [spark-streaming](https://github.com/pndaproject/example-applications/tree/master/spark-streaming): example streaming data processing application
   * [jupyter-notebooks](https://github.com/pndaproject/example-applications/tree/master/jupyter-notebooks): examples for working with Jupyter notebooks
   * [kafka-spark-opentsdb](https://github.com/pndaproject/example-applications/tree/master/kafka-spark-opentsdb): example consumer that feeds data to OpenTSDB
 * [example-kafka-clients](https://github.com/pndaproject/example-kafka-clients): examples for working with kafka clients
   * [java](https://github.com/pndaproject/example-kafka-clients/tree/master/java)
   * [php](https://github.com/pndaproject/example-kafka-clients/tree/master/php)
   * [python](https://github.com/pndaproject/example-kafka-clients/tree/master/python)
 
### Documentation

 * [pnda-guide](README.md): this guide
 
## [References](others/README.md)

## [Release Notes](https://github.com/pndaproject/pnda/tree/master/releases)

## Versions

This guide is the latest version and describes the software found on the pndaproject develop branches. 

To refer to guides for specific releases, please navigate to the relevant release tag on [github](https://github.com/pndaproject/pnda-guide).

