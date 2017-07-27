# Hadoop Distributions

## Choice of Hadoop Distribution
PNDA can be provisioned with either [Hortonworks HDP](https://hortonworks.com/products/data-center/hdp/) or [Cloudera CDH](https://www.cloudera.com/products/open-source/apache-hadoop/key-cdh-components.html) as the Hadoop distribution

The Hadoop distribution provides the main data storage and data processing capabilities. The distribution brings together all the upstream component projects that make up 'Hadoop' in a tested package, with pre-built binaries and APIs for automated setup.

## How to select the distribution
First you need to decide based on feature set, licencing or pricing, which distribution to use. See below for a basic overview of the differences.

In terms of physically selecting the distribution, the Hadoop distribution to use is configured at PNDA creation time as a single setting in the pnda_env.yaml file. The PNDA setup instructions cover this at the appropriate point.

The PNDA mirror (which provides all the resources required during PNDA creation) contains both CDH and HDP components, so there is no need to select one or the other when creating the PNDA mirror.

## Hortonworks HDP
 - Is 100% open source
 - Uses Amabri for cluster monitoring, management, [additional UIs](https://docs.hortonworks.com/HDPDocuments/Ambari-2.5.1.0/bk_ambari-views/content/ch_understanding_ambari_views.html) and [setup](https://cwiki.apache.org/confluence/display/AMBARI/Blueprints)
 - Does not provide [Impala](https://impala.apache.org/) (use [Hive](https://hortonworks.com/blog/apache-hive-vs-apache-impala-query-performance-comparison/) instead). A performance evaluation would be advisable for the specific workloads you will run, if this type of SQL query is important to your use cases.
 - [Licensing](https://hortonworks.com/services/support/enterprise/) is required for support.

## Cloudera CDH & Cloudera Manager
 - Cloudera CDH is 100% open source
 - [Cloudera Manager](https://www.cloudera.com/products/product-components/cloudera-manager.html) and some other components are proprietary
 - Uses Cloudera Manager for cluster monitoring, management and [setup](https://cloudera.github.io/cm_api/)
 - Uses Hue for [additional UIs](http://gethue.com/)
 - Provides Impala for MPP SQL queries
 - [Licencing](https://www.cloudera.com/content/dam/www/static/documents/datasheets/cloudera-enterprise-datasheet.pdf) is required for support and advanced cluster management features.
