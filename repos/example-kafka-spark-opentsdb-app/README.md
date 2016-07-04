# Example Streaming Application: Kafka to OpenTSDB

## Overview

This example application reads events from Kafka and writes them to OpenTSDB.

The application is a tarball file containing binaries and configuration files required to perform stream processing.

#### PNDA Guide

The chapter "Packages & Applications" in the PNDA guide contains a lot more detail on the Deployment Manager, Console and Repository Manager. You can also work through the step by step guide to using PNDA in "Getting Started".

## Requirements

* [SBT](http://www.scala-sbt.org/0.13/docs/Setup.html) 0.13.8 or later
* [Java JDK](https://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html) 1.8 or later

## Build standalone jar

This project is built with sbt. See the [install instructions](http://www.scala-sbt.org/release/docs/Setup.html).

	sbt assembly

## Creating the package

The previous step generate the jar file. To create a package, you will need a set of files, which are available in the src/universal folder:

- `application.properties`: config file used by the Spark Streaming scala application.
- `log4j.properties`: defines the log level and behaviour for the application.
- `opentsdb.json`: contains metrics to be created in OpenTSDB.
- `properties.json`: contains default properties that may be overridden at application creation time.
- `upstart.conf`: runs the spark streaming job as a supervised upstart service.
- `yarn-kill.py`: called by upstart.conf to kill the yarn job when stopping the upstart service.

We use the sbt native packager for creating the package. For more information: [SBT Native packager](http://www.scala-sbt.org/sbt-native-packager/). To generate the tarball, run:

	 sbt universal:packageZipTarball

Your package will be available into the target/universal folder.

## Deploying the package and creating an application

The PNDA console can be used to deploy the application package to a cluster and then to create an application instance. The console is available on port 80 on the edge node.

When creating an application in the console, ensure that the `input_topic` property is set to a real Kafka topic.

```
"input_topic": "avro.kso.metrics",
```

To make the package available for deployment, it must be uploaded to a package repository. The default implementation is an OpenStack Swift container. The package may be uploaded via the PNDA repository manager which abstracts the container used, or by manually uploading the package to the container.

## Run sample data source

If you want to produce test data and see how the ingest pipeline works, there is a script in `data-source/producer.py` which produces random events and sends it over Kafka.

To run the test script, refer to the instructions in the `data-source` folder of this repository.
