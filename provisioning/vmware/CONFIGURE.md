# Configure PNDA creation process

![](../images/breadcrumbs-cfg.jpg)

## Introduction

The PNDA creation process is controlled primarily via a YAML configuration file.

A template YAML configuration can be found in the [pnda-cli repository](https://github.com/pndaproject/pnda-cli). 

## Configure pnda_env.yaml

#### Designate client machine

Create or designate a suitable machine for running the PNDA CLI. We recommend CentOS 7.

#### Obtain code

Clone the [pnda-cli repository](https://github.com/pndaproject/pnda-cli) repository from the master branch at a specific release tag (e.g. ```release/4.0```) to the client machine.

Copy ```pnda_env_example.yaml``` to create ```pnda_env.yaml```

#### Set access credentials

Set the following fields under `terraform_parameters` section in `pnda_env.yaml` .

| Field | Value |
| --- | --- |
|VS_USER| vSphere user for creating PNDA |
|VS_PASSWORD| vSphere password for VS_USER |
|VS_DS| vSphere data store to use for this deployment |
|VS_PUBLIC_NETWORK| ID of the network to place PNDA VMs on |
|VS_SERVER| vSphere API endpoint | 
|VS_TEMPLATE_xxx| VM Images to use for various node types | 
|TF_ROOT_USER| Username with root ssh login to the VS_TEMPLATE_xxx images |
|TF_ROOT_PASSWORD| Password for TF_ROOT_USER |

As part of the provisioning process ssh login for TF_ROOT_USER is disabled and replaced with key-based login for a user named 'cloud-user'.

#### Hadoop distribution

Decide whether you want to run the Cloudera CDH or the Hortonworks HDP Hadoop distribution.

Set `hadoop.HADOOP_DISTRO` to either `CDH` or `HDP`.

#### Set source of SaltStack provisioning scripts

The PNDA software is installed and configured using the SaltStack code found in the [platform-salt](https://github.com/pndaproject/platform-salt) repository. There are two main options to provide source for platform-salt:

1. Set `platform_salt.PLATFORM_GIT_REPO_URI` to the remote git URI and `platform_salt.PLATFORM_GIT_BRANCH` at the specified branch to be cloned during provisioning.
If authenticated access to `platform_salt.PLATFORM_GIT_REPO_URI` is required, then place the ssh key to use, named git.pem, in the top level directory of "pnda-cli" repository and also set `platform_salt.PLATFORM_GIT_REPO_HOST` to the hostname of the server.

2. A local copy of platform-salt can be used by setting (`platform_salt.PLATFORM_SALT_LOCAL`) to the path to the platform-salt folder on the local machine running pnda-cli.py.

#### PNDA mirror

Set `mirrors.PNDA_MIRROR` to the URI determined by the placement of the mirror and build components in the staging phase.

#### Other fields

There are a wide range of parameters that can be set, please refer to ```pnda_env_example.yaml``` in the [pnda-cli repository](https://github.com/pndaproject/pnda-cli) for more details.

#### SSH key pair

Create an ssh keypair with `ssh-keygen -t rsa -N '' -f key_name` to use when creating the virtual machine instances for PNDA as ```key_name```. 

Place both parts of the key root of the pnda-cli directory named as follows: the private key ```key_name.pem``` and public key ```key_name.pub```. 

Ensure that key_name.pem has 0600 permissions. 

# [Next](CREATE.md)

| [Home](../OVERVIEW.md) | [Prepare](PREPARE.md) | [Image](IMAGE.md) | [Configure](CONFIGURE.md) | [Create](CREATE.md) | 
| --- | --- | --- | --- | --- | 
