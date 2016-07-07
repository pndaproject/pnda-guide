# Platform requirements

PNDA is designed to be deployed on an [OpenStack](https://www.openstack.org/) cloud computing infrastructure. This guide assumes that you are familiar with OpenStack, and that you have an environment set up in which you can create instances, whether in a public or private cloud.

## Requirements

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

A typical standard PNDA cluster has the following requirements. However, you are strongly encouraged to create a PNDA flavor specifically designed for your infrastructure.

### Cluster configuration

| Role | Flavor | Number required |
| --- | --- | --- |
|  `Manager1Flavor`| `C`|1|
|  `Manager2Flavor`| `A`|1|
|  `Manager3Flavor`| `A`|1|
|  `Manager4Flavor`| `A`|1|
|  `LogserverFlavor`| `H`|1|
|  `EdgeFlavor`| `G`|1|
|  `JupyterFlavor`| `B`|1|
|  `OpentsdbFlavor`| `E`|2|
|  `ToolsFlavor`| `F`|1|
|  `ZookeeperFlavor`| `F`|3|
|  `KafkaFlavor`| `D`|2|
|  `DatanodeFlavor`| `A`|3|
|  `BastionFlavor`| `I`|1|
|  `SaltmasterFlavor`| `I`|1|
|  `DatanodeVolumeSize`| 1024|per DN|

Instances are defined with the following sizes:

|Flavor|vCPUs|Memory|Storage|
|-----|--:|------:|-------:|
| `A` | 8 | 32 GB | 250 GB |
| `B` | 8 | 16 GB | 250 GB |
| `C` | 4 | 16 GB | 250 GB |
| `D` | 4 | 16 GB |  50 GB |
| `E` | 4 |  8 GB |  50 GB |
| `F` | 2 |  8 GB |  50 GB |
| `G` | 1 |  4 GB | 250 GB |
| `H` | 2 |  4 GB | 500 GB |
| `I` | 1 |  2 GB |  20 GB |

## OpenStack Heat vs. Salt Cloud

The primary way of provisioning a PNDA cluster is with [OpenStack Heat](../repos/pnda-heat-templates/README.md). You can run Heat on your own computer, but it only works in the OpenStack environment.

Alternatively, you can use [Salt Cloud](saltstack.md) to provision a cluster. In this case, you must manually create a salt master instance, and then run salt remotely on that instance.
