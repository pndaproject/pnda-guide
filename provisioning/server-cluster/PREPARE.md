# Select & Prepare Platform

![](../images/breadcrumbs.jpg)

## Commission server cluster

The target server cluster must have adequate resources for deploying the chosen PNDA flavor in terms of numbers of nodes, CPU, memory and storage capacity.

Building and configuring a suitable server cluster is outside the scope of this guide. Quite frequently technologies such as [Cobbler](http://cobbler.github.io/) are used to automate this process.

## Required resources

In addition to the existing PNDA flavors, [described elsewhere in this guide](https://github.com/pndaproject/pnda-guide/blob/develop/provisioning/aws/PREPARE.md), we have defined a flavor specifically intended for creating PNDA on physical server clusters.  

#### Production

Production flavor is intended for a reasonably sized physical cluster. It runs the core services in high-availability mode and provides reasonable storage space and compute resource.

| Role | Number required | Cores | Memory | Storage
| --- | --- | --- | --- | --- | 
|  `tools`         | 1 | 16 |  64 GB   | 1TB
|  `edge`         | 1 | 20 |  256 GB   | 1TB
|  `mgr1`         | 1 | 16 |  128 GB   | 1TB
|  `mgr2`         | 1 | 16 |  128 GB   | 1TB
|  `mgr3`         | 1 | 16 |  128 GB   | 1TB
|  `mgr4`         | 1 | 16 |  128 GB   | 1TB
|  `datanode`         | 3 | 24 |  128 GB   | As required
|  `opentsdb`         | 2 | 16 |  64 GB   | 1TB
|  `hadoop-manager`   | 1 | 16 |  64 GB   | 1TB
|  `kafka`         | 3 | 20 |  128 GB   | As required
|  `zookeeper`         | 3 | 16 |  64 GB   | 1TB

We recommended dividing the storage on every node into at least a root volume (100GB is sufficient) and a data volume. We also recommend JBOD for Hadoop datanodes and RAID10 for Kafka. However, all these aspects are configurable.

## Firewall setup
The firewall needs to allow TCP and UDP (DNS service) ports within the PNDA Cluster, or firewall needs to be disabled.


# [Next](MIRROR.md)

| [Home](../OVERVIEW.md) | [Prepare](PREPARE.md) | [Mirror](MIRROR.md) | [Build](BUILD.md) | [Stage](STAGE.md) | [Configure](CONFIGURE.md) | [Create](CREATE.md) | 
| --- | --- | --- | --- | --- | --- | --- |
