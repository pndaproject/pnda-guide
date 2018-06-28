# Configure PNDA creation process

![](../images/breadcrumbs-cfg.jpg)

## Introduction

The PNDA creation process is controlled primarily via a YAML configuration file.

A template YAML configuration can be found in the [Heat templates repository](https://github.com/pndaproject/pnda-heat-templates). 

## Configure pnda_env.yaml

#### Designate client machine

Create or designate a suitable machine for running the PNDA CLI. We recommend CentOS 7.

#### Obtain code

Clone the [Heat templates repository](https://github.com/pndaproject/pnda-heat-templates) repository from the master branch at a specific release tag (e.g. ```release/3.5```) to the client machine.

Copy ```pnda_env_example.yaml``` to create ```pnda_env.yaml```

#### Set access credentials

Set the following fields. The values can be obtained by referring to the Keystone authentication details obtained in the preparation phase.

| Field | Value |
| --- | --- |
|keystone_user| User for creating PNDA |
|keystone_password| Password for user |
|keystone_tenant| Tenant for creating PNDA |
|keystone_auth_url| Authorization URL | 
|keystone_auth_version| 2 or 3 |
|keystone_region_name| Region name |

#### Object storage

Set `pnda_apps_container` to the Application container configured during the preparation phase.

Set `pnda_apps_folder` to the Application folder configured during the preparation phase.

Set `pnda_archive_container` to the Dataset archive container configured during the preparation phase.

#### Hadoop distribution

Decide whether you want to run the Cloudera CDH or the Hortonworks HDP Hadoop distribution.

Set `hadoop_distro` to either `CDH` or `HDP`.

#### Set source of SaltStack provisioning scripts

The PNDA software is installed and configured using the SaltStack code found in the [platform-salt](https://github.com/pndaproject/platform-salt) repository.  This must be supplied via a URI to a git repository.

Set `platform_git_repo_uri` to the required git URI at the specified branch during provisioning.

If authenticated access to `platform_git_repo_uri` is required then place the private SSH key to use, named ```deploy```, in the top level of the pnda-heat-templates repository.

**Note** that by default the master branch of the specified git repository is used in provisioning. See below for other fields that can be used to control this behaviour.

#### PNDA mirror

Set `PndaMirror` to the URI determined by the placement of the mirror and build components in the staging phase.

#### Other fields

There are a wide range of parameters that can be set, please refer to ```pnda_env_example.yaml``` in the [Heat templates repository](https://github.com/pndaproject/pnda-heat-templates) for more details.

## Security Material

#### Perimeter security (FQDN's and associated certificates/private keys)
Access to the PNDA cluster requires user authentication over a secure connection. In order to secure this user authentication, the perimeter servers require certification material which allows validating the FQDN used to access those servers to further authenticate and secure the connection to those servers.

For PRODUCTION ENVIRONMENTS, this security material MUST be generated outside the PNDA realm and dropped under the ./platform-certificates directory tree. Consult the README files under that same directory and sub-directories for further details on the required material.

For NON-PRODUCTION ENVIRONMENTS, a helper tool (./tools/gen-certs.py) is provided that can auto-generate the required server certificates based on an existing CA (private key) or based on a newly generated CA (when no private key is detected in the ./platform-certificates directory by the helper tool).

# [Next](CREATE.md)

| [Home](../OVERVIEW.md) | [Prepare](PREPARE.md) | [Mirror](MIRROR.md) | [Build](BUILD.md) | [Stage](STAGE.md) | [Configure](CONFIGURE.md) | [Create](CREATE.md) | 
| --- | --- | --- | --- | --- | --- | --- | 
