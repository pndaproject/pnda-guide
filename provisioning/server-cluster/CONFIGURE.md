# Configure PNDA creation process

![](../images/breadcrumbs-cfg.jpg)

## Introduction

The PNDA creation process is executed over SSH using key based authentication, and controlled via a YAML configuration file.

A template YAML configuration can be found in the [PNDA CLI repository](https://github.com/pndaproject/pnda-cli). 

## Designate client machine

Create or designate a suitable machine for running the PNDA CLI. We recommend CentOS 7.

## Obtain code

Clone the [PNDA CLI repository](https://github.com/pndaproject/pnda-cli) repository from the master branch at a specific release tag (e.g. ```release/3.5```) to the client machine.

Copy ```pnda_env_example.yaml``` to create ```pnda_env.yaml```

## SSH key pair

Create an SSH key pair for use when configuring the PNDA nodes as ```key_name```. 

```sh
ssh-keygen -b 2048 -t rsa -f key_name.pem -q -N ""
```

Place the private key ```key_name.pem``` in the root of the pnda-cli directory. 

Ensure that key_name.pem has 0600 permissions. 

## Configure sudo user

Create a sudo user `<username>` on each machine:

```sh
sudo su
adduser <username> --disabled-password --gecos ""
echo <username> ALL=NOPASSWD: ALL >> /etc/sudoers
```
Allow login for that user with the created key on each machine:

```sh
mkdir /home/<username>/.ssh
cat key_name.pem.pub >> /home/<username>/.ssh/authorized_keys
```

## Configure pnda_env.yaml

##### Set the OS user to be used in provisioning

Set `OS_USER` to the appropriate value.

##### Configure local storage of applications

Set `pnda_application_repo: PR_FS_TYPE` to `local`.

##### Configure NTP server

Set `ntp: NTP_SERVERS` to an appropriate value for your cluster.

##### Hadoop distribution

Decide whether you want to run the Cloudera CDH or the Hortonworks HDP Hadoop distribution.

Set `hadoop.HADOOP_DISTRO` to either `CDH` or `HDP`.

##### Set source of SaltStack provisioning scripts

The PNDA software is installed and configured using the SaltStack code found in the [platform-salt](https://github.com/pndaproject/platform-salt) repository.  This can be supplied in two main ways.

##### Use local copy of platform-salt

A local copy of platform-salt can be used by setting `platform_salt.PLATFORM_SALT_LOCAL` to the path to the platform-salt folder on the client machine.

##### Git repository

Set `platform_salt.PLATFORM_GIT_REPO_URI` and `platform_salt.PLATFORM_GIT_BRANCH` to clone a remote git URI at the specified branch during provisioning.
  
If authenticated access to `platform_salt.PLATFORM_GIT_REPO_URI` is required then place the ssh key to use, named git.pem, in the top level directory of this repository and set `platform_salt.PLATFORM_GIT_REPO_HOST` to the hostname of the server.

**Note** Please ensure that the local clone of platform-salt or  `platform_salt.PLATFORM_GIT_BRANCH` correspond to the same release tag as the pnda-cli repository cloned above.

##### PNDA mirror

Set `mirrors.PNDA_MIRROR` to the URI determined by the placement of the mirror and build components in the staging phase.

##### Other fields

There are a wide range of parameters that can be set, please refer to ```pnda_env_example.yaml``` in the [PNDA CLI repository](https://github.com/pndaproject/pnda-cli) for more details.

## Cluster descriptor

Edit existing-machines/production.json (or a copy of this file) with the IP addresses for the machines in the cluster. ip_address must be reachable from each machine but does not have to be reachable from where the cli is run. Add or remove datanodes, kafka nodes, zookeeper nodes and opentsdb nodes to match the number of machines that you have.

## Volume descriptor

Review the [volume config mapping](https://github.com/pndaproject/pnda-cli/blob/develop/bootstrap-scripts/production/volume-config.yaml) for your flavor so the number of disk volumes you have matches the ones being requested. By default the volumes are assigned out in descending size order, but it is possible to specify the device names and hard code the mappings in the volume config file. The disks can also be partitioned automatically. For an example of specifying device names and partitioning see [this volume config file](https://github.com/pndaproject/pnda-cli/blob/develop/bootstrap-scripts/production/volume-config.yaml).

# [Next](CREATE.md)

| [Home](../OVERVIEW.md) | [Prepare](PREPARE.md) | [Mirror](MIRROR.md) | [Build](BUILD.md) | [Stage](STAGE.md) | [Configure](CONFIGURE.md) | [Create](CREATE.md) | 
| --- | --- | --- | --- | --- | --- | --- |
