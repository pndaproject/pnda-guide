# Getting Started

This guide will help you get started setting up a fully operational PNDA cluster. The main tasks can be grouped as follows: 

- [Selecting a Hadoop distribution](hadoop_distro.md)
- [Creating PNDA](#creating-pnda)
- [Basic data exploration](#basic-data-exploration)
- [Producer integration](#producer-integration)
- [Packages and applications](#packages-and-applications)

Some of these tasks may be performed by different people in your organization. For example, enterprise IT staff may be responsible for provisioning a cluster, software developers may  write producers and consumers to process data, and data scientists may analyze collected data. 

## Creating PNDA

1. Carefully follow the [PNDA creation guide](../provisioning/OVERVIEW.md) for your target environment.
2. Launch the [console](../console/README.md) to make sure that everything is running. You can connect to `http://clustername-hadoop-edge`, where `clustername` is the name of your cluster. 

## Basic data exploration

In this tutorial, you will learn how to generate a set of sample data, bulk upload it into the PNDA platform, and explore the data using notebooks.  

1. Open the [console](../console/README.md), and click the Jupyter link. Upload the [example notebook](https://github.com/pndaproject/example-applications/tree/master/jupyter-notebooks), and follow the instructions in the notebook.
2. In the [example notebook](https://github.com/pndaproject/example-applications/tree/master/jupyter-notebooks), follow the "instructions" section to use the data generation tool or embedded cell to generate test data sets.
3. Use the [bulk ingest tool](https://github.com/pndaproject/platform-tools/tree/master/bulkingest) to import the test data sets.
4. Use the sample notebook to analyze the data. 

For an in-depth tutorial, see the [Interactive exploratory data analytics](../exploration/lab.md) lab.

## Producer integration
 
In the basic data exploration tutorial you generated sample data in a single step. In a real-world scenario, however, the data of interest is likely being generated in real-time. This data needs to be captured using producers that direct it topics configured on Kafka. For this tutorial, we will use a producer based on Logstash.

For the purposes of this guide, we'll use the test data source for the example [Spark Streaming](https://github.com/pndaproject/example-applications/tree/master/spark-streaming) application.

1. Select a host to act as the producer. It needs to have network connectivity with Kafka.
2. Create a topic on Kafka using the [Kafka Manager](https://github.com/yahoo/kafka-manager). Once created, the topic will appear in the [console](../console/README.md).
3. Install and configure [Logstash](https://github.com/elastic/logstash) on the chosen host then install the following [plugin](../producer/logstash.md).
4. Run the test data script. In the `data-source` directory of the [example-spark-streaming](https://github.com/pndaproject/example-applications/tree/master/spark-streaming) repository, there is a python script that can send a stream of data over TCP into logstash. 
5. In the [console](../console/README.md), verify that data is flowing into the topics. In the left-hand column, you should see activity on your Kafka topic.
6. After a period of time (up to 30 minutes), the master dataset will be automatically created by [gobblin](https://github.com/pndaproject/gobblin), and you will be able to see it in the [Datasets](../console/datasets.md) page in the console.

## Packages and applications

1. A pre-requisite to working with packages is to ensure that the application package repository is correctly configured as described in the [PREPARE phase depending your infra: AWS, OpenStack or server clusters](../provisioning/OVERVIEW.md).
2. Build and upload the [spark-streaming](https://github.com/pndaproject/example-applications/tree/master/spark-streaming) app to the package repository. Upload the file to the Object Store via the [platform-package-repository](https://github.com/pndaproject/platform-package-repository).
3. In the console, [deploy the package](../console/packages.md). On the packages page, look for the package you have just uploaded in the list of available packages. (If you don't see it, try clicking the refresh button.) Click the deploy button next to the package, and you'll see it added to the list of deployed packages. 
4. Create an [application](../console/applications.md) from the package. On the applications page, click "Create New Application", select the package you have just deployed, and follow the prompts to create a new application. 
5. Start the application. On the applications page, click the Start button next to the application. When the application is running, the button will change to Pause.
6. If you click on the application, and then click on the metrics tab, you should see metrics appear that indicate the time taken to process batches of data. 
7. Open [Impala](../query/impala.md) and see if your data is there. `ssh` to the edge node (indicated on the [console](../console/README.md) home page) and run `impala-shell` to query the data in `example_table`. The default table name is `example_table`, if you didn't change it when creating the application. Impala follows the SQL-92 standard, with some extensions, detailed in the [Impala SQL reference](http://www.cloudera.com/documentation/enterprise/5-5-x/topics/impala_langref.html). For example, try `select count(*) from example_table;`.

For in-depth tutorials on Spark Streaming, see the [Spark Streaming and HBase](../applications/ksh.md) and [Spark Streaming and OpenTSDB](../applications/kso.md) tutorials.
