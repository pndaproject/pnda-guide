# Repositories

The PNDA distribution is available on GitHub at:

 * [https://github.com/pndaproject](https://github.com/pndaproject)

It consists of the following source code repositories and sub-projects:

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
 
