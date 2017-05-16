# PNDA Guide

PNDA is a simple, scalable, open big data platform supporting operational and business intelligence analysis for networks and services. This guide provides an overview of PNDA, and will tell you how to set up and use PNDA in your own environment. 

## Quick Links

- [Website](http://pandaproject.io)
- [Guide](http://pandaproject.io/guide)
- [GitHub](https://github.com/pndaproject)
- [Questions & Answers](http://pandaproject.io/qa)
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

## [Provisioning](provisioning/README.md)

This chapter describes how to provision a PNDA cluster, and includes some background information on SaltStack, OpenStack Heat and AWS CloudFormation.

 * [Creating PNDA](OVERVIEW.md)
 
### Background information

 * [Getting started with Heat](heat.md)
 * [Getting started with AWS](aws.md)
 * [Getting started with SaltStack](saltstack.md)
 
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

## [Producers](producer/README.md)

Kafka is the "front door" of PNDA. It handles ingest of data streams from network sources and distributes data to all interested consumers. This chapter covers how to integrate and develop "producers", which feed data into Kafka.

 * [Preparing data](producer/data-preparation.md)
 * [Integrating Logstash](producer/logstash.md)
 * [Integrating OpenDaylight](producer/opendl.md)
 * [Integrating OpenBMP](producer/openbmp.md)
 * [Integrating Pmacct](producer/pmacct.md)
 * [Developing a custom producer](producer/producer.md)

## [Bulk Ingest](bulkingest/README.md)

In addition to streaming ingest via Kafka producers, PNDA also provides an offline bulk ingest tool for those who would like to migrate pre-existing data into the PNDA platform. 

 * [Bulk-ingest tool](repos/platform-tools/bulkingest/README.md)

## [Consumers](consumer/README.md)

Kafka has a simple, clean design that moves complexity traditionally found inside message brokers into its producers and consumers. A Kafka consumer pulls messages from one or more topics using Zookeeper for discovery, issuing fetch requests to the  brokers leading the partitions it wants to consume. Rather than the broker maintaining state and controlling the flow of data, each consumer controls the rate at which it consumes messages.

## [Packages & Applications](applications/README.md)

Packages are independently deployable units of application layer functionality, and applications are instances of packages. You can use the PNDA console to deploy packages and manage the application lifecycle. The Deployment Manager documentation explains the structure of packages, and the REST API used to deploy them. 

 * [Deployment Manager](repos/platform-deployment-manager/README.md)
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

## [Time Series](timeseries/README.md)

OpenTSDB is a scalable time series database that lets you store and serve massive amounts of time series data, without losing granularity. Grafana is a graph and dashboard builder for visualizing time series metrics.

* [OpenTSDB](timeseries/opentsdb.md)
* [Grafana](timeseries/grafana.md)

## [Security](security/README.md)

A big data infrastructure like PNDA involves a multitude of technologies and tools, and may be deployed in a multi-tenant environment. Providing enterprise grade security for such system is not only complex, but is of primary concern for any production deployment. If you are implementing a client for a PNDA interface or developing a PNDA application, this chapter will cover some security guidelines that you should adhere to when working with individual components. 

## [Repositories](repos/README.md)

The PNDA distribution consists of the following source code repositories and sub-projects:

### Provisioning

 * [platform-salt](repos/platform-salt/README.md): provisioning logic for creating PNDA
 * [platform-salt-cloud](repos/platform-salt-cloud/README.md): cluster templates for creating PNDA with salt-cloud
 * [pnda-heat-templates](repos/pnda-heat-templates/README.md): cluster templates for creating PNDA with Heat
 * [pnda-aws-templates](repos/pnda-aws-templates/README.md): cluster templates for creating PNDA with CloudFormation on AWS
 * [pnda-dib-elements](repos/pnda-dib-elements/README.md): tools for building disk image templates

### Platform

 * [platform-libraries](repos/platform-libraries/README.md): libraries for working with interactive notebooks
 * [platform-tools](repos/platform-tools/README.md): tools for operating a cluster
     * [bulkingest](repos/platform-tools/bulkingest/README.md): tools for performing a bulk ingest of data
 * [platform-console-frontend](repos/platform-console-frontend/README.md): “single pane of glass” giving operational overview and access to application and data management functions
 * [platform-console-backend](repos/platform-console-backend/README.md): APIs that provide data to the console frontend
   * [console-backend-data-logger](repos/platform-console-backend/console-backend-data-logger/README.md): APIs to ingest data
   * [console-backend-data-manager](repos/platform-console-backend/console-backend-data-manager/README.md): APIs to provide data
 * [platform-testing](repos/platform-testing/README.md): modules that test both the end to end platform and individual components and collect metrics
 * [platform-deployment-manager](repos/platform-deployment-manager/README.md): API to manage packages and application deployment and lifecycle
 * [platform-data-mgmnt](repos/platform-data-mgmnt/README.md): tools to manage data retention
   * [data-service](repos/platform-data-mgmnt/data-service/README.md): API to set data retention policies
   * [hdfs-cleaner](repos/platform-data-mgmnt/hdfs-cleaner/README.md): cron job to clean up HDFS data
   * [oozie-templates](repos/platform-data-mgmnt/oozie-templates/README.md): templates that archive or delete data
 * [platform-package-repository](repos/platform-package-repository/README.md): manages a simple package repository backed by OpenStack Swift

### Forked Projects

 * [gobblin](repos/gobblin/README.md): customized fork of the Gobblin data ingest frameworkjup

### Producers

 * [prod-odl-kafka](repos/prod-odl-kafka/README.md): plugin to ingest data from OpenDaylight
 * [logstash-codec-pnda-avro](https://github.com/pndaproject/logstash-codec-pnda-avro): patched AVRO codec ingest data from Logstash

### Examples

 * [example-applications](repos/example-applications/README.md): example applications that can be built and run on PNDA
   * [spark-batch](repos/example-applications/spark-batch/README.md): example batch data processing application
   * [spark-streaming](repos/example-applications/spark-streaming/README.md): example streaming data processing application
   * [jupyter-notebooks](repos/example-applications/jupyter-notebooks/README.md): examples for working with Jupyter notebooks
   * [kafka-spark-opentsdb](repos/example-applications/kafka-spark-opentsdb/README.md): example consumer that feeds data to OpenTSDB
 * [example-kafka-clients](repos/example-kafka-clients/README.md): examples for working with kafka clients
   * [java](repos/example-kafka-clients/java/README.md)
   * [php](repos/example-kafka-clients/php/README.md)
   * [python](repos/example-kafka-clients/python/README.md)
 
### Documentation

 * [pnda-guide](README.md): this guide
 
## [References](others/README.md)

## Release Notes

From PNDA 3.3, please refer to the pnda repository for all release notes.

 * [pnda](https://github.com/pndaproject/pnda): pnda release notes and build system

## Versions

This guide is the latest version and describes the software found on the pndaproject develop branches. To refer to guides for specific releases, please navigate to the relevant release tag on [github](https://github.com/pndaproject/pnda-guide).

