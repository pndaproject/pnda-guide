# Hadoop Distributions

## Choice of Hadoop Distribution
PNDA can be provisioned with either [Hortonworks HDP](https://hortonworks.com/products/data-center/hdp/) or [Cloudera CDH](https://www.cloudera.com/products/open-source/apache-hadoop/key-cdh-components.html) as the Hadoop distribution

The Hadoop distribution provides the main data storage and data processing capabilities. The distribution brings together all the upstream component projects that make up 'Hadoop' in a tested package, with pre-built binaries and APIs for automated setup.

## How to select the distribution
First you need to decide based on feature set, licencing or pricing, which distribution to use. See below for a basic overview of the differences.

In terms of physically selecting the distribution, the Hadoop distribution to use is configured at PNDA creation time as a single setting in the pnda_env.yaml file. The PNDA setup instructions cover this at the appropriate point.

The PNDA mirror (which provides all the resources required during PNDA creation) contains both CDH and HDP components, so there is no need to select one or the other when creating the PNDA mirror.

## Hortonworks HDP
 - [Hortonworks HDP](https://hortonworks.com/products/data-center/hdp/) is 100% open source
 - Uses Amabri for cluster monitoring, management, [additional UIs](https://docs.hortonworks.com/HDPDocuments/Ambari-2.5.1.0/bk_ambari-views/content/ch_understanding_ambari_views.html) and [setup](https://cwiki.apache.org/confluence/display/AMBARI/Blueprints)
 - Provides Hive for MPP SQL queries instead of Impala (a performance evaluation would be advisable for the specific workloads you will run, if this type of SQL query is important to your use cases).
 - A [commercial subscription](https://hortonworks.com/services/support/enterprise/) is required for support.

## Cloudera CDH & Cloudera Manager
 - [Cloudera CDH](https://www.cloudera.com/products/open-source/apache-hadoop/key-cdh-components.html) is 100% open source
 - [Cloudera Manager](https://www.cloudera.com/products/product-components/cloudera-manager.html) and some other components are [proprietary](https://www.cloudera.com/content/dam/www/static/documents/datasheets/cloudera-enterprise-datasheet.pdf) (certain core features may be used for free but advanced cluster management features require a commercial licence)
 - Uses Cloudera Manager for cluster monitoring, management and [setup](https://cloudera.github.io/cm_api/)
 - Uses Hue for [additional UIs](http://gethue.com/)
 - Provides Impala for MPP SQL queries
 - A [commercial subscription](https://www.cloudera.com/more/services-and-support.html) is required for support.

## NOTE 

All information provided about Hortonworks and Cloudera products and services is for convenience purposes only and may not reflect current licencing or pricing. Please visit or contact Hortonworks and Cloudera directly to determine your rights and obligations.
