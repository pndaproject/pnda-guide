# Cloud platform requirements

PNDA is designed to be deployed on bare metal servers, [OpenStack](https://www.openstack.org/) cloud computing infrastructure or on Amazon Web Services (AWS). This guide assumes that you are familiar with OpenStack, and that you have an environment set up in which you can create instances, whether in a public or private cloud. Or in the case of AWS, have an account with AWS.

## Openstack Requirements

- PNDA is supported on OpenStack [Kilo](http://releases.openstack.org) or later.
- Instances are created using [Ubuntu](http://www.ubuntu.com) version 14.04.
- PNDA is deployed using the [Heat](../repos/pnda-heat-templates/README.md) orchestration service, using `heat_template_version: 2014-10-16`. Alternatively, you can use [Salt Cloud](saltstack.md).
- The cluster should be set up with one network and one router, and have the possibility to provision multiple virtual machines. See below.
- The cluster must have access to the public Internet for installation of dependencies.

## OpenStack Swift

PNDA expects two Swift containers to be present in Swift. These must be created prior to launching a cluster. The OpenStack Horizon console can be used to create Swift containers, by navigating to `Object Store > Containers > Create Container`.

### Application Packages

Application packages are expected to be found in a pseudo-folder named `releases` in a Swift container called `apps`. This will be shared by all PNDA clusters for distributing application packages.

The Swift container and pseudo-folder path within the Swift container can be configured using the `pnda.apps_container` and `pnda.apps_folder` settings in the salt pillar.

### Data Archive

Data from PNDA data sets can be archived to Swift automatically. A Swift container must be created in Swift to be used for this purpose. By default a swift container called `archive` should be created but this can be configured using the `pnda.archive_container` setting in the salt pillar.

## Resources
The resource requirements for the default pico and standard flavor PNDA clusters are detailed below. However, you are strongly encouraged to create a PNDA flavor specifically designed for your infrastructure.

### HEAT
#### Cluster configuration: Pico

Pico flavor is intended for development / learning purposes. It is fully functional, but does not run the core services in high-availability mode and does not provide much storage space or compute resource.

| Role | Number required | CPUs | Memory | Storage
| --- | --- | --- | --- | --- |
|  `bastion`   | 1 | 1 |  2 GB | 20 GB
|  `saltmaster`| 1 | 1 |  2 GB | 20 GB
|  `edge`      | 1 | 4 | 10 GB | 30 GB
|  `mgr1`      | 1 | 4 | 8 GB | 30 GB
|  `datanode`  | 1 | 4 | 8 GB | 65 GB
|  `kafka`     | 1 | 2 | 8 GB | 30 GB
| -  |  - | -  |  - | -  |
|  `total`     | 6 | 16 | 38 GB | 198 GB

The storage per node is allocated as:
 - 10 GB log volume (not present on bastion or saltmaster). This is provision-time configurable.
 - 20 GB operating system partition. This is configured in the templates per-node.
 - 35 GB HDFS (only on datanode). This is configured in the templates for the datanode.

#### Cluster configuration: Standard

Standard flavor is intended for meaningful PoC and investigations at scale. It runs the core services in high-availability mode and provides reasonable storage space and compute resource.

| Role | Number required | CPUs | Memory | Storage
| --- | --- | --- | --- | --- |
|  `bastion`   | 1 | 1 |  2 GB |   50 GB
|  `saltmaster`| 1 | 1 |  2 GB |   50 GB
|  `edge`      | 1 | 1 |  4 GB |  370 GB
|  `mgr1`      | 1 | 8 | 32 GB |  370 GB
|  `mgr2`      | 1 | 8 | 32 GB |  370 GB
|  `mgr3`      | 1 | 8 | 32 GB |  370 GB
|  `mgr4`      | 1 | 8 | 32 GB |  370 GB
|  `datanode`  | 3 | 8 | 32 GB | 1194 GB
|  `opentsdb`  | 2 | 4 |  8 GB |  50 GB
|  `cloudera-manager`| 1 | 4 | 8 GB |  170 GB
|  `jupyter`   | 1 | 8 | 16 GB | 50 GB
|  `logserver` | 1 | 2 |  4 GB | 500 GB
|  `kafka`     | 2 | 4 | 16 GB | 270 GB
|  `zookeeper` | 3 | 2 |  8 GB | 170 GB
|  `tools`     | 1 | 2 |  8 GB | 50 GB
| -  |  - | -  |  - | -  |
|  `total`     | 21 | 97 | 340 GB | 7.3TB

The storage per node is allocated as:
 - 120 GB log volume (not present on bastion, saltmaster, jupyter, tools or opentsdb). This is provision-time configurable.
 - 1024 GB HDFS (only on datanode). This is configured in the templates for the datanode.
 - 50-250 GB operating system partition. This is configured in the templates per-node.

## OpenStack Heat vs. Salt Cloud

The primary way of provisioning a PNDA cluster on OpenStack cloud is with [OpenStack Heat](../repos/pnda-heat-templates/README.md).

Alternatively, you can use [Salt Cloud](saltstack.md) to provision a cluster. In this case, you must manually create a salt master instance, and then run salt remotely on that instance. Please refer to [this guide](saltstack.md) for details.

### AWS

#### Cluster configuration: Pico

Pico flavor is intended for development / learning purposes. It is fully functional, but does not run the core services in high-availability mode and does not provide much storage space or compute resource.

| Role | Instance type | Number required | CPUs | Memory | Storage
| --- | --- | --- | --- | --- | --- | --- |
|  `bastion`   |  t2.medium  | 1 | 2 |  4 GB   | 20 GB
|  `edge`      |  m3.xlarge  | 1 | 4 | 15 GB   | 30 GB
|  `mgr1`      |  m3.xlarge  | 1 | 4 | 15 GB   | 30 GB
|  `datanode`  |  c4.xlarge  | 1 | 4 |  7.5 GB | 65 GB
|  `kafka`     |  m3.large   | 1 | 2 |  7.5 GB | 30 GB
| -  |  - | -  | -  | -  | -  |
|  `total`     |  | 5 | 16 | 49 GB | 198 GB

The storage per node is allocated as:
 - 10 GB log volume (not present on bastion or saltmaster). This is provision-time configurable.
 - 20 GB operating system partition. This is configured in the templates per-node.
 - 35 GB HDFS (only on datanode). This is configured in the templates for the datanode.

#### Cluster configuration: Standard

Standard flavor is intended for meaningful PoC and investigations at scale. It runs the core services in high-availability mode and provides reasonable storage space and compute resource.

| Role | Instance type | Number required | CPUs | Memory | Storage
| --- | --- | --- | --- | --- | --- | --- |
|  `bastion`         |  t2.medium  | 1 | 2 |  4 GB   | 50 GB
|  `saltmaster`      |  m3.large   | 1 | 2 |  7.5 GB | 50 GB
|  `edge`            |  t2.medium  | 1 | 2 |  4 GB   | 370 GB
|  `mgr1`            |  m3.2xlarge | 1 | 8 |  30 GB  | 370 GB
|  `mgr2`            |  m3.2xlarge | 1 | 8 |  30 GB  | 370 GB
|  `mgr3`            |  m3.2xlarge | 1 | 8 |  30 GB  | 370 GB
|  `mgr4`            |  m3.2xlarge | 1 | 8 |  30 GB  | 370 GB
|  `datanode`        |  m4.2xlarge | 3 | 8 |  32 GB  | 1194 GB
|  `opentsdb`        |  m3.xlarge  | 2 | 4 | 15 GB   | 50 GB
|  `cloudera-manager`|  m3.xlarge  | 1 | 4 | 15 GB   | 170 GB
|  `jupyter`         |  m3.large   | 1 | 2 |  7.5 GB | 50 GB
|  `logserver`       |  m3.large   | 1 | 2 |  7.5 GB | 500 GB
|  `kafka`           |  m3.xlarge  | 2 | 4 | 15 GB   | 270 GB
|  `zookeeper`       |  m3.large   | 3 | 2 |  7.5 GB | 170 GB
|  `tools`           |  m3.large   | 1 | 2 |  7.5 GB | 50 GB
| -  |  - | -  |  - | -  | -  |
|  `total`           |   | 21 | 94 |  352 GB | 7.3TB

The storage per node is allocated as:
 - 120 GB log volume (not present on bastion, saltmaster, jupyter, tools or opentsdb). This is provision-time configurable.
 - 1024 GB HDFS (only on datanode). This is configured in the templates for the datanode.
 - 50-250 GB operating system partition. This is configured in the templates per-node.


