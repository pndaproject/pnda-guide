# Repositories

The PNDA distribution is available on GitHub at:

 * [https://github.com/pndaproject](https://github.com/pndaproject)

It consists of the following source code repositories and sub-projects:

## Provisioning

 * [platform-salt](platform-salt/README.md): provisioning logic for creating PNDA
 * [platform-salt-cloud](platform-salt-cloud/README.md): cluster templates for creating PNDA with salt-cloud
 * [pnda-heat-templates](pnda-heat-templates/README.md): cluster templates for creating PNDA with Heat
 * [pnda-dib-elements](pnda-dib-elements/README.md): tools for building disk image templates

## Platform

 * [platform-libraries](platform-libraries/README.md): libraries for working with interactive notebooks
 * [platform-tools](platform-tools/README.md): tools for operating a cluster
     * [bulkingest](platform-tools/bulkingest/README.md): tools for performing a bulk ingest of data
 * [platform-console-frontend](platform-console-frontend/README.md): “single pane of glass” giving operational overview and access to application and data management functions
 * [platform-console-backend](platform-console-backend/README.md): APIs that provide data to the console frontend
   * [console-backend-data-logger](platform-console-backend/console-backend-data-logger/README.md): APIs to ingest data
   * [console-backend-data-manager](platform-console-backend/console-backend-data-manager/README.md): APIs to provide data
 * [platform-testing](platform-testing/README.md): modules that test both the end to end platform and individual components and collect metrics
 * [platform-deployment-manager](platform-deployment-manager/README.md): API to manage packages and application deployment and lifecycle
 * [platform-data-mgmnt](platform-data-mgmnt/README.md): tools to manage data retention
   * [data-service](platform-data-mgmnt/data-service/README.md): API to set data retention policies
   * [hdfs-cleaner](platform-data-mgmnt/hdfs-cleaner/README.md): cron job to clean up HDFS data
   * [oozie-templates](platform-data-mgmnt/oozie-templates/README.md): templates that archive or delete data
 * [platform-package-repository](platform-package-repository/README.md): manages a simple package repository backed by OpenStack Swift

## Forked projects

 * [gobblin](gobblin/README.md): customized fork of the Gobblin data ingest framework

## Producers

 * [prod-odl-kafka](prod-odl-kafka/README.md): plugin to ingest data from OpenDaylight
 * [prod-logstash-codec-avro](prod-logstash-codec-avro/README.md): plugin to ingest data from Logstash

## Examples

 * [example-applications](repos/example-applications/README.md): example applications that can be built and run on PNDA
   * [spark-batch](repos/example-applications/spark-batch/README.md): example batch data processing application
   * [spark-streaming](repos/example-applications/spark-streaming/README.md): example streaming data processing application
   * [jupyter-notebooks](repos/example-applications/jupyter-notebooks/README.md): examples for working with Jupyter notebooks
   * [kafka-spark-opentsdb](repos/example-applications/kafka-spark-opentsdb/README.md): example consumer that feeds data to OpenTSDB
 * [example-kafka-clients](repos/example-kafka-clients/README.md): examples for working with kafka clients
   * [java](repos/example-kafka-clients/java/README.md)
   * [php](repos/example-kafka-clients/php/README.md)
   * [python](repos/example-kafka-clients/python/README.md)

## Documentation

 * [pnda-guide](../README.md): this guide
