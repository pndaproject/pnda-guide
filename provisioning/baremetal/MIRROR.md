# Create PNDA mirror components

![](../images/breadcrumbs-mirror.jpg)

## Introduction

As many real-world deployment environments don’t have Internet connectivity and online sources are not always dependable, PNDA is created from a cache of all the required software known as the "PNDA mirror".

At provisioning time, when Minions execute SaltStack states, software from this server is downloaded, installed and configured.

Before PNDA can be created, first we must create the directory structure and file sets to be placed on the PNDA mirror.

### Ubuntu or RHEL

PNDA can be created on Ubuntu or RHEL instances. Before building the PNDA mirror components, decide which instance type your deployment will use.

## Create mirror

#### Select build machine

Designate or create the mirror build machine. 
 
This can be a physical machine or a VM but it needs to reflect the type of mirror you wish to build and must be clean.

Two types of mirror are supported -

- Red Hat Enterprise Linux 7
- Ubuntu 14.04 

#### Obtain mirror build tools

The repository [pnda](https://github.com/pndaproject/pnda) contains all the tools needed to create and maintain the mirror file sets.

Clone this repository to the mirror creation machine. The tools are found in the [mirror folder](https://github.com/pndaproject/pnda/tree/master/mirror).

#### Build mirror file sets

To run the entire mirror creation process -

```sh
sudo su
./create_mirror.sh
```

The script automatically detects the host Linux distribution and builds the appropriate file sets.

This takes about 20 minutes to run and the output will be available in a directory named ```mirror-dist```.

##### Building parts of the mirror

The different parts of the mirror can be created separately if required. The scripts to do this are -

```
create_mirror_deb.sh
create_mirror_rpm.sh
create_mirror_misc.sh
create_mirror_python.sh
create_mirror_anaconda.sh
create_mirror_cdh.sh
```

Note that, as above, the deb and rpm scripts are for use on Ubuntu or RHEL hosts respectively.

Each script creates it's output in a directory named for the respective mirror type -

```
mirror_deb
mirror_rpm
mirror_misc
mirror_python
mirror_anaconda
mirror_cloudera
```

For more about creating and maintaining mirrors, please refer to the [repository notes](https://github.com/pndaproject/pnda/tree/master/mirror).

# [Next](BUILD.md)

| [Home](../OVERVIEW.md) | [Prepare](PREPARE.md) | [Undercloud](UNDERCLOUD.md) | [Saltmaster](SALTMASTER.md) | [Register](REGISTER.md) | [Mirror](MIRROR.md) | [Build](BUILD.md) | [Stage](STAGE.md) | [Configure](CONFIGURE.md) | [Create](CREATE.md) | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
