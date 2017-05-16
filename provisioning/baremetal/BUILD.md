# Create PNDA build components

![](../images/breadcrumbs-build.jpg)

## Introduction

In addition to projects like Hadoop and Kafka, PNDA also includes a variety of components that provide an operations console, application deployment and more. Build these components before provisioning PNDA.

## Create build machine

#### Select build machine

Designate or create the PNDA build machine. This could be the same machine that was used to build the mirror file sets.
 
Two types of build node are supported -

- Red Hat Enterprise Linux 7
- Ubuntu 14.04 


#### Obtain build tools

The repository [pnda](https://github.com/pndaproject/pnda) contains all the tools needed to build PNDA.

Clone this repository to the build machine. The tools are found in the [build folder](https://github.com/pndaproject/pnda).

#### Preparing the build environment

The script ```install-build-tools.sh``` installs all the necessarily build prerequisites.

Run it with superuser privileges in the location that you wish to install your build tools.

For example

```sh
sudo su
cd /home/builder
./install-build-tools.sh
```

As well as installing all the required software, it may pause and ask the operator to carry out some configuration on the build environment, for example adjusting the contents of /etc/hosts.

Once it has finished, a file called ```set-pnda-env.sh``` will be found in the working directory. This script contains the necessary environment variables and other settings needed to carry out builds. It should either be added to the end of an initialization script such as ```/etc/bash.bashrc``` so that these settings are available for new shells, or it can be invoked with each build. 

For example

```sh
sudo su
cat >> /etc/bash.bashrc
. /home/builder/set-pnda-env.sh
```

Your environment is now ready to build PNDA.

## Building PNDA

The script ```build-pnda.sh``` is invoked as a non-privileged user. 

For example

```sh
cd pnda
./build-pnda.sh RELEASE release/3.5
```

It is also possible to perform more complex builds including building to a specific bill-of-materials. Please refer to the [repository notes](https://github.com/pndaproject/pnda).

## Build Products

All build products are assembled in the directory ```pnda-dist```.

# [Next](STAGE.md)

| [Home](../OVERVIEW.md) | [Prepare](PREPARE.md) | [Undercloud](UNDERCLOUD.md) | [Saltmaster](SALTMASTER.md) | [Register](REGISTER.md) | [Mirror](MIRROR.md) | [Build](BUILD.md) | [Stage](STAGE.md) | [Configure](CONFIGURE.md) | [Create](CREATE.md) | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
