# Cloud platform requirements

PNDA can be deployed on bare metal servers, [OpenStack](https://www.openstack.org/) cloud computing infrastructure or on [Amazon Web Services](https://aws.amazon.com/).

- PNDA has been tested on versions of OpenStack between [Kilo](http://releases.openstack.org) and [Newton](http://releases.openstack.org).

This guide assumes that you are familiar with these platforms and have an environment set up in which you can create instances.

## Object Store

PNDA makes use of object storage for -

- Storing and delivering user-created application packages
- Archiving PNDA datasets that have reached age or size thresholds

### S3 and Swift

PNDA uses Amazon S3 when deploying to AWS and Swift when deploying to OpenStack. If creating your own OpenStack platform, please ensure that you deploy Swift.

PNDA requires specific containers and folders to be created in object storage. Please do this before creating a PNDA cluster.

| Container or folder | Reference (AWS) | Reference (OpenStack) |
| --- | --- | --- |
| Application container | [PNDA_APPS_CONTAINER](https://github.com/pndaproject/pnda-aws-templates/blob/master/pnda_env_example.yaml) | [pnda_apps_container](https://github.com/pndaproject/pnda-heat-templates/blob/master/pnda_env_example.yaml) | 
| Application folder inside container | [PNDA_APPS_FOLDER](https://github.com/pndaproject/pnda-aws-templates/blob/master/pnda_env_example.yaml) | [pnda_apps_folder](https://github.com/pndaproject/pnda-heat-templates/blob/master/pnda_env_example.yaml) |
| Dataset archive container | [PNDA_ARCHIVE_CONTAINER](https://github.com/pndaproject/pnda-aws-templates/blob/master/pnda_env_example.yaml) | [pnda_archive_container](https://github.com/pndaproject/pnda-heat-templates/blob/master/pnda_env_example.yaml) |

## Resources

The resource requirements for the default pico and standard flavor PNDA clusters are detailed below. However, you are strongly encouraged to create a PNDA flavor specifically designed for your specific infrastructure.

### HEAT
#### Cluster configuration: Pico

Pico flavor is intended for development / learning purposes. It is fully functional, but does not run the core services in high-availability mode and does not provide much storage space or compute resource.

| Role | Instance type | Number required | CPUs | Memory | Total Storage | Root Volume Storage | Log Volume Storage
| --- | --- | --- | --- | --- | --- | --- | --- |
|  `bastion`   |  ec2.t2.medium  | 1 | 2 |  4 GB   | 20 GB | 20 GB | 0 GB
|  `saltmaster`   |  ec2.t2.medium  | 1 | 2 |  4 GB   | 20 GB | 20 GB | 0 GB
|  `edge`      |  ec2.m3.xlarge  | 1 | 4 | 15 GB   | 30 GB | 20 GB | 10 GB
|  `mgr1`      |  ec2.m3.xlarge  | 1 | 4 | 15 GB   | 30 GB | 20 GB | 10 GB
|  `datanode`  |  ec2.c4.xlarge  | 1 | 4 |  7.5 GB | 65 GB | 20 GB | 10 GB
|  `kafka`     |  ec2.m3.large   | 1 | 2 |  7.5 GB | 30 GB | 20 GB | 10 GB
| -  |  - | -  | -  | -  | -  |
|  `total`     |  | 6 | 18 | 53 GB | 195 GB

The storage per node is allocated as:
 - 10 GB log volume (not present on bastion or saltmaster). This is provision-time configurable.
 - 20 GB operating system partition. This is configured in the templates per-node.
 - 35 GB HDFS (only on datanode). This is configured in the templates for the datanode.

#### Cluster configuration: Standard

Standard flavor is intended for meaningful PoC and investigations at scale. It runs the core services in high-availability mode and provides reasonable storage space and compute resource.

| Role | Instance type | Number required | CPUs | Memory | Total Storage | Root Volume Storage | Log Volume Storage
| --- | --- | --- | --- | --- | --- | --- | --- |
|  `bastion`         |  ec2.t2.medium  | 1 | 2 |  4 GB   | 50 GB | 50 GB | 0 GB
|  `saltmaster`      |  ec2.m3.large   | 1 | 2 |  7.5 GB | 50 GB | 50 GB | 0 GB
|  `edge`            |  ec2.t2.medium  | 1 | 2 |  4 GB   | 370 GB | 250 GB | 120 GB
|  `mgr1`            |  ec2.m3.2xlarge | 1 | 8 |  30 GB  | 370 GB | 250 GB | 120 GB
|  `mgr2`            |  ec2.m3.2xlarge | 1 | 8 |  30 GB  | 370 GB | 250 GB | 120 GB
|  `mgr3`            |  ec2.m3.2xlarge | 1 | 8 |  30 GB  | 370 GB | 250 GB | 120 GB
|  `mgr4`            |  ec2.m3.2xlarge | 1 | 8 |  30 GB  | 370 GB | 250 GB | 120 GB
|  `datanode`        |  ec2.m4.2xlarge | 3 | 8 |  32 GB  | 1194 GB | 50 GB | 120 GB
|  `opentsdb`        |  ec2.m3.xlarge  | 2 | 4 | 15 GB   | 50 GB | 50 GB | 0 GB
|  `cloudera-manager`|  ec2.m3.xlarge  | 1 | 4 | 15 GB   | 170 GB | 50 GB | 120 GB
|  `jupyter`         |  ec2.m3.large   | 1 | 2 |  7.5 GB | 50 GB | 50 GB | 0 GB
|  `logserver`       |  ec2.m3.large   | 1 | 2 |  7.5 GB | 500 GB | 500 GB | 0 GB
|  `kafka`           |  ec2.m3.xlarge  | 2 | 4 | 15 GB   | 270 GB | 150 GB | 120 GB
|  `zookeeper`       |  ec2.m3.large   | 3 | 2 |  7.5 GB | 170 GB | 50 GB | 120 GB
|  `tools`           |  ec2.m3.large   | 1 | 2 |  7.5 GB | 50 GB | 50 GB | 0 GB
| -  |  - | -  |  - | -  | -  |
|  `total`           |   | 21 | 94 |  352 GB | 7.3TB

The storage per node is allocated as:
 - 120 GB log volume (not present on bastion, saltmaster, jupyter, tools or opentsdb). This is provision-time configurable.
 - 1024 GB HDFS (only on datanode). This is configured in the templates for the datanode.
 - 50-250 GB operating system partition. This is configured in the templates per-node.

### AWS

#### Cluster configuration: Pico

Pico flavor is intended for development / learning purposes. It is fully functional, but does not run the core services in high-availability mode and does not provide much storage space or compute resource.

| Role | Instance type | Number required | CPUs | Memory | Storage
| --- | --- | --- | --- | --- | --- |
|  `bastion`   |  t2.medium  | 1 | 2 |  4 GB   | 20 GB
|  `edge`      |  m3.xlarge  | 1 | 4 | 15 GB   | 30 GB
|  `mgr1`      |  m3.xlarge  | 1 | 4 | 15 GB   | 30 GB
|  `datanode`  |  c4.xlarge  | 1 | 4 |  7.5 GB | 65 GB
|  `kafka`     |  m3.large   | 1 | 2 |  7.5 GB | 30 GB
| -  |  - | -  | -  | -  | -  |
|  `total`     |  | 5 | 16 | 49 GB | 175 GB

The storage per node is allocated as:
 - 10 GB log volume (not present on bastion or saltmaster). This is provision-time configurable.
 - 20 GB operating system partition. This is configured in the templates per-node.
 - 35 GB HDFS (only on datanode). This is configured in the templates for the datanode.

#### Cluster configuration: Standard

Standard flavor is intended for meaningful PoC and investigations at scale. It runs the core services in high-availability mode and provides reasonable storage space and compute resource.

| Role | Instance type | Number required | CPUs | Memory | Storage
| --- | --- | --- | --- | --- | --- |
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
