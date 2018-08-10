# Select & Prepare Platform

![](../images/breadcrumbs.jpg)

## VMWare prerequisites

Building and configuring VMWare is outside the scope of this guide. Please note the following -

- PNDA is regularly tested on [VMWare vSphere 6.5](https://www.vmware.com/products/vsphere.html) but is expected to work on versions of vSphere from 5.5 onwards.
- There is no orchestration available as part of VMWare, but this will be covered later using [Terraform](https://www.terraform.io/).
- Before continuing, please ensure -
	- At least one suitable network / Datastore /  exists
	- It provides sufficient resources and quota to provision one of the flavors described below

## Required resources

The resource requirements for the default pico and standard flavor PNDA clusters are detailed below. However, you are strongly encouraged to create a PNDA flavor specifically designed for your infrastructure.

#### Pico

Pico flavor is intended for development / learning purposes. It is fully functional, but does not run the core services in high-availability mode and does not provide much storage space or compute resource.

| Role | Instance type | Number required | CPUs | Memory | Storage
| --- | --- | --- | --- | --- | --- |
|  `gateway`   |  t2.medium  | 1 | 2 |  4 GB   | 40 GB
|  `edge`      |  m4.2xlarge  | 1 | 8 | 32 GB   | 50 GB
|  `mgr1`      |  m4.xlarge  | 1 | 4 | 16 GB   | 50 GB
|  `datanode`  |  c4.xlarge  | 1 | 4 |  7.5 GB | 65 GB
|  `kafka`     |  m4.large   | 1 | 2 |  8 GB | 50 GB
| -  |  - | -  | -  | -  | -  |
|  `total`     |  | 5 | 20 | 67,5 GB | 255 GB

The storage per node is allocated as:
 - 10 GB log volume (not present on gateway or saltmaster). This is provision-time configurable.
 - 20 GB operating system partition. This is configured in the templates per-node.
 - 35 GB HDFS (only on datanode). This is configured in the templates for the datanode.

#### Standard

Standard flavor is intended for meaningful PoC and investigations at scale. It runs the core services in high-availability mode and provides reasonable storage space and compute resource.

| Role | Instance type | Number required | CPUs | Memory | Storage
| --- | --- | --- | --- | --- | --- |
|  `gateway`         |  t2.medium  | 1 | 2 |  4 GB   | 170 GB
|  `saltmaster`      |  m4.large   | 1 | 2 |  8 GB | 50 GB
|  `edge`            |  t2.medium  | 1 | 2 |  4 GB   | 370 GB
|  `mgr1`            |  m4.2xlarge | 1 | 8 |  32 GB  | 370 GB
|  `mgr2`            |  m4.2xlarge | 1 | 8 |  32 GB  | 370 GB
|  `mgr3`            |  m4.2xlarge | 1 | 8 |  32 GB  | 370 GB
|  `mgr4`            |  m4.2xlarge | 1 | 8 |  32 GB  | 370 GB
|  `datanode`        |  m4.2xlarge | 3 | 8 |  32 GB  | 1394 GB
|  `opentsdb`        |  m4.xlarge  | 2 | 4 | 16 GB   | 170 GB
|  `hadoop-manager`  |  m4.xlarge  | 1 | 4 | 16 GB   | 170 GB
|  `jupyter`         |  m4.large   | 1 | 2 |  8 GB | 170 GB
|  `logserver`       |  m4.large   | 1 | 2 |  8 GB | 500 GB
|  `kafka`           |  m4.xlarge  | 2 | 4 | 16 GB   | 270 GB
|  `zookeeper`       |  m4.large   | 3 | 2 |  8 GB | 170 GB
|  `tools`           |  m4.large   | 1 | 2 |  8 GB | 50 GB
| -  |  - | -  |  - | -  | -  |
|  `total`           |   | 21 | 94 |  368 GB | 7.7TB

The storage per node is allocated as:
 - 120 GB log volume (not present on gateway, saltmaster or tools). This is provision-time configurable.
 - 1024 GB HDFS (only on datanode). This is configured in the templates for the datanode.
 - 50-250 GB operating system partition. This is configured in the templates per-node.

# [Next](IMAGE.md)

| [Home](../OVERVIEW.md) | [Prepare](PREPARE.md) | [Image](IMAGE.md) | [Configure](CONFIGURE.md) | [Create](CREATE.md) | 
| --- | --- | --- | --- | --- | 
