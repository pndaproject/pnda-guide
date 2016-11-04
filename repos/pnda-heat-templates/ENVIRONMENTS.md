# PNDA stack parameters, flavors and environmnent files

## Stack Parameters
### PNDA flavor
The PNDA flavor defines the distribution of roles across instances.

* **PndaFlavor:**

| *Mandatory:* | yes |
| -------------|-----|
| *Purpose:* | Flavor for the PNDA deployment. |
| *Type:* | string |
| *Default value:* | None |
| *Example value:* | 'standard' |
| *Valid values:* | [ standard ] |

### Swift containers
A PNDA deployment makes use of swift containers to backup applications and packages on a regular basis. These containers shall be created in a tenant prior to the PNDA stack creation process.
* **pnda_apps_container:**

| *Mandatory:* | yes |
| -------------|-----|
| *Purpose:* | container name containing PNDA deployed applications. |
| *Type:* | string |
| *Default value:* | None |
| *Example value:* | 'apps' |

* **pnda_archive_container:**

| *Mandatory:* | yes |
| -------------|-----|
| *Purpose:* | name of the container handling PNDA archives. |
| *Type:* | string |
| *Default value:* | None |
| *Example value:* | 'archives' |

* **pnda_apps_folder:**

| *Mandatory:* | yes |
| -------------|-----|
| *Purpose:* | name of the folder within the apps container handling application files. |
| *Type:* | string |
| *Default value:* | None |
| *Example value:* | 'releases' |

### Instance Flavors
Defining the virtual machines flavors (size)
* **EdgeFlavor:**

| *Mandatory:* | no |
| -------------|-----|
| *Purpose:* | Flavor of the EDGE instance |
| *Type:* | string |
| *Default value:* | m1.xlarge |
| *Example value:* | m1.large |
| *PNDA flavors* | standard |

* **BastionFlavor:**

| *Mandatory:* | no |
| -------------|-----|
| *Purpose:* | Flavor of the BASTION instance |
| *Type:* | string |
| *Default value:* | m1.large |
| *Example value:* | m1.large |
| *PNDA flavors* | standard |

* **SaltmasterFlavor:**

| *Mandatory:* | no |
| -------------|-----|
| *Purpose:* | Flavor of the SALT MASTER instance |
| *Type:* | string |
| *Default value:* | m1.large |
| *Example value:* | m1.large |
| *PNDA flavors* | standard |

* **CMFlavor:**

| *Mandatory:* | no |
| -------------|-----|
| *Purpose:* | Flavor of the CLOUDERA MANAGER instance |
| *Type:* | string |
| *Default value:* | m1.xlarge |
| *Example value:* | m1.xlarge |
| *PNDA flavors* | standard |

* **Manager1Flavor:**

| *Mandatory:* | no |
| -------------|-----|
| *Purpose:* | Flavor of the MANAGER1 instance |
| *Type:* | string |
| *Default value:* | m1.xlarge |
| *Example value:* | m1.xlarge |
| *PNDA flavors* | standard |

* **Manager2Flavor:**

| *Mandatory:* | no |
| -------------|-----|
| *Purpose:* | Flavor of the MANAGER2 instance |
| *Type:* | string |
| *Default value:* | m1.xlarge |
| *Example value:* | m1.xlarge |
| *PNDA flavors* | standard |

* **Manager3Flavor:**

| *Mandatory:* | no |
| -------------|-----|
| *Purpose:* | Flavor of the MANAGER3 instance |
| *Type:* | string |
| *Default value:* | m1.xlarge |
| *Example value:* | m1.xlarge |
| *PNDA flavors* | standard |

* **Manager4Flavor:**

| *Mandatory:* | no |
| -------------|-----|
| *Purpose:* | Flavor of the MANAGER4 instance |
| *Type:* | string |
| *Default value:* | m1.xlarge |
| *Example value:* | m1.xlarge |
| *PNDA flavors* | standard |

* **LogserverFlavor:**

| *Mandatory:* | no |
| -------------|-----|
| *Purpose:* | Flavor of the LOGSERVER instance |
| *Type:* | string |
| *Default value:* | m1.large |
| *Example value:* | m1.large |
| *PNDA flavors* | [ standard ] |

* **ToolsFlavor:**

| *Mandatory:* | no |
| -------------|-----|
| *Purpose:* | Flavor of the TOOLS instance |
| *Type:* | string |
| *Default value:* | m1.large |
| *Example value:* | m1.large |
| *PNDA flavors* | standard |

* **OpentsdbFlavor:**

| *Mandatory:* | no |
| -------------|-----|
| *Purpose:* | Flavor of the OPENTSDB instance |
| *Type:* | string |
| *Default value:* | m1.large |
| *Example value:* | m1.large |
| *PNDA flavors* | standard |

* **DatanodeFlavor:**

| *Mandatory:* | no |
| -------------|-----|
| *Purpose:* | Flavor of the DATANODE instance |
| *Type:* | string |
| *Default value:* | m1.xlarge |
| *Example value:* | m1.xlarge |
| *PNDA flavors* | standard |

* **JupyterFlavor:**

| *Mandatory:* | no |
| -------------|-----|
| *Purpose:* | Flavor of the JUPYTER instance |
| *Type:* | string |
| *Default value:* | m1.xlarge |
| *Example value:* | m1.xlarge |
| *PNDA flavors* | [ standard ] |

* **KafkaFlavor:**

| *Mandatory:* | no |
| -------------|-----|
| *Purpose:* | Flavor of the KAFKA instance |
| *Type:* | string |
| *Default value:* | m1.large |
| *Example value:* | m1.large |
| *PNDA flavors* | standard |

* **ZookeeperFlavor:**

| *Mandatory:* | no |
| -------------|-----|
| *Purpose:* | Flavor of the ZOOKEEPER instance |
| *Type:* | string |
| *Default value:* | m1.large |
| *Example value:* | m1.large |
| *PNDA flavors* | standard |

* **PkgserverFlavor:**

| *Mandatory:* | no |
| -------------|-----|
| *Purpose:* | Flavor of the PKGSERVER instance |
| *Type:* | string |
| *Default value:* | m1.medium |
| *Example value:* | m1.medium |
| *PNDA flavors* | standard |

### Cluster sizing
Define the cluster sizes (instances count) as part of the PNDA deployment.
* **ZookeeperNodes:**

| *Mandatory:* | no |
| -------------|-----|
| *Purpose:* | Number of ZOOKEEPER instances |
| *Type:* | string |
| *Default values:* | standard:3 |
| *Example value:* | 3 |

* **KafkaNodes:**

| *Mandatory:* | no |
| -------------|-----|
| *Purpose:* | Number of KAFKA instances |
| *Type:* | string |
| *Default values:* | standard:1 |
| *Example value:* | 3 |

* **OpentsdbNodes:**

| *Mandatory:* | no |
| -------------|-----|
| *Purpose:* | Number of OPENTSDB instances |
| *Type:* | string |
| *Default values:* | standard:1 |
| *Example value:* | 3 |

* **DataNodes:**

| *Mandatory:* | no |
| -------------|-----|
| *Purpose:* | Number of Hadoop DATANODE instances |
| *Type:* | string |
| *Default values:* | standard:1 |
| *Example value:* | 3 |

### Networking
PNDA is deployed on top of a private network, providing isolation. These parameters define this created network's properties.
* **private_net_cidr:**

| *Mandatory:* | no |
| -------------|-----|
| *Purpose:* | Created private network cidr |
| *Type:* | string |
| *Default values:* | 192.168.10.0/24 |
| *Example value:* | 192.168.0.0/24 |

* **private_net_pool_start:**

| *Mandatory:* | no |
| -------------|-----|
| *Purpose:* | First DHCP address available |
| *Type:* | string |
| *Default values:* | 192.168.10.10 |
| *Example value:* | 192.168.0.10 |

* **private_net_pool_end:**

| *Mandatory:* | no |
| -------------|-----|
| *Purpose:* | Last DHCP address available |
| *Type:* | string |
| *Default values:* | 192.168.10.250 |
| *Example value:* | 192.168.0.250 |

* **private_net_gateway:**

| *Mandatory:* | no |
| -------------|-----|
| *Purpose:* | Created private network gateway |
| *Type:* | string |
| *Default values:* | 192.168.10.1 |
| *Example value:* | 192.168.0.1 |

* **public_net:**

| *Mandatory:* | yes |
| -------------|-----|
| *Purpose:* | UUID of the external network |
| *Type:* | string |
| *Default values:* | None |
| *Example value:* | de671752-cc2d-4251-9ac9-fc71d334c497 |

* **name_servers:**

| *Mandatory:* | yes |
| -------------|-----|
| *Purpose:* | Array of name servers ip addresses |
| *Type:* | string |
| *Default values:* | None |
| *Example value:* | ['8.8.8.8', '8.8.4.4'] |

### Swift Authentication
Access to the created swift containers from the deployed PNDA instances requires authentication.
* **keystone_auth_url**

| *Mandatory:* | yes |
| -------------|-----|
| *Purpose:* | Keystone API URL for authentication |
| *Type:* | string |
| *Default values:* | None |
| *Example value:* | 'http://host.example.com:5000/v2.0' |

* **keystone_region_name**

| *Mandatory:* | yes |
| -------------|-----|
| *Purpose:* | Region name in which the tenant stands |
| *Type:* | string |
| *Default values:* | None |
| *Example value:* | 'regionOne' |

* **keystone_tenant**

| *Mandatory:* | yes |
| -------------|-----|
| *Purpose:* | Tenant in which the container exist |
| *Type:* | string |
| *Default values:* | None |
| *Example value:* | 'pnda_tenant' |

* **keystone_user**

| *Mandatory:* | yes |
| -------------|-----|
| *Purpose:* | User name for keystone authentication |
| *Type:* | string |
| *Default values:* | None |
| *Example value:* | 'pnda_user' |

* **keystone_password**

| *Mandatory:* | yes |
| -------------|-----|
| *Purpose:* | Password of the user for keystone authentication |
| *Type:* | string |
| *Default values:* | None |
| *Example value:* | '9048318784200444' |

### Storage
Each Hadoop data node gets an attached volume
* **DatanodeMountpoint**

| *Mandatory:* | no |
| -------------|-----|
| *Purpose:* | block device to which to attach the created volume |
| *Type:* | string |
| *Default values:* | '/dev/vdb' |
| *Example value:* | '/dev/vdb' |

* **DatanodeVolumeSize**

| *Mandatory:* | no |
| -------------|-----|
| *Purpose:* | Size in GB of the created volume |
| *Type:* | string |
| *Default values:* | 200 |
| *Example value:* | 1024 |

### Deployment from git
When deploying, one can choose git repo sources for salt recipes. Both http and ssh are supported to clone the platform-salt repository. If ssh is used, a private key may be necessary. Its value has to be stored in a file, specified by the _git_private_key_file_ parameter. Defining the _platform_git_repo_uri_ supersedes the definition of the parameters for deployment from tarballs.

* **platform_git_repo_uri**

| *Mandatory:* | no |
| -------------|-----|
| *Purpose:* | URI for the platform-salt git repository |
| *Type:* | string |
| *Default values:* | None |
| *Example value:* | 'git@github.cisco.com:PNDA/platform-salt.git' |

* **GitBranch**

| *Mandatory:* | no / yes if platform_git_repo_uri is specified |
| -------------|-----|
| *Purpose:* | git branch of platform-salt to checkout |
| *Type:* | string |
| *Default values:* | None |
| *Example value:* | 'master' |

* **git_private_key_file**

| *Mandatory:* | no / yes if platform_git_repo_uri is specified and ssh is used |
| -------------|-----|
| *Purpose:* | file containing the private key value necessary to clone the platform-salt repository |
| *Type:* | string |
| *Default values:* | None |
| *Example value:* | 'master' |

### Deployment from tarballs releases
When deploying, one can choose to deploy from tarball files (releases) rather than from the git repository. the _platform_uri_ parameter is superseded by the _platform_git_repo_uri_ definition. Meaning that if _platform_git_repo_uri_ is defined it git based deployment takes precedence.

* **platform_uri**

| *Mandatory:* | no / yes if platform_git_repo_uri is not defined |
| -------------|-----|
| *Purpose:* | URI of the platform-salt release tarball |
| *Type:* | string |
| *Default values:* | None |
| *Example value:* | 'http://www.example.com/platform_salt_release-0.1.tar.gz' |

* **packages_server_uri**

| *Mandatory:* | yes |
| -------------|-----|
| *Purpose:* | URI of the packages location |
| *Type:* | string |
| *Default values:* | None |
| *Example value:* | 'http://www.example.com/packages' |

### Mirror usage
Installing the Cloudera Hadoop cluster might take some time. In an effort to reduce the provisioning time it is possible to specify mirrors for Hadoop parcels or the Oracle JDK installer. If not defined, Installation will grab the Cloudera parcels or the JDK installer from the Cloudera and Oracle servers, respectively.

* **ClouderaParcelsMirror**

| *Mandatory:* | no |
| -------------|-----|
| *Purpose:* | URI of the Cloudera parcels mirror |
| *Type:* | string |
| *Default values:* | None |
| *Example value:* | 'http://www.example.com/mirror/archive.cloudera.com/cdh5/parcels/5.5.2/' |

* **AnacondaParcelsMirror**

| *Mandatory:* | no |
| -------------|-----|
| *Purpose:* | URI of the Anaconda parcels mirror |
| *Type:* | string |
| *Default values:* | None |
| *Example value:* | 'http://www.example.com/mirror/anaconda/misc/parcels/' |

* **NtpServers**

| *Mandatory:* | no |
| -------------|-----|
| *Purpose:* | NTP server |
| *Type:* | string |
| *Default values:* | None |
| *Example value:* | 'europe.pool.ntp.org' |

* **JavaMirror**

| *Mandatory:* | no |
| -------------|-----|
| *Purpose:* | URI of Oracle JDK installer mirror |
| *Type:* | string |
| *Default values:* | None |
| *Example value:* | 'http://www.example.com/java/jdk/8u74-b02/jdk-8u74-linux-x64.tar.gz' |

### VM instances image
PNDA instances are created from the image created using the pnda-dib-elements.

* **image_id**

| *Mandatory:* | yes |
| -------------|-----|
| *Purpose:* | UUID or name of the PNDA base image |
| *Type:* | string |
| *Default values:* | 'pnda-base' |
| *Example value:* | 'pnda-base' |

### ssh access to the Deployment
When deploying PNDA attach an existing keypair to the bastion instance. This keypair is necessary to connect to the instance through ssh.

* **KeyName**

| *Mandatory:* | yes |
| -------------|-----|
| *Purpose:* | keypair name of an existing key pair |
| *Type:* | string |
| *Default values:* | None |
| *Example value:* | 'pnda_user' |

### Support for cluster scaling
When scaling a PNDA deployment (adding nodes) it is necessary to pass a deployment unique identifier, which will force the orchestration step for the cloudera clusters.

* **DeploymentID**

| *Mandatory:* | no, unless resizing the deployment |
| -------------|-----|
| *Purpose:* | trigerring the Cloudera orchestration/scaling process |
| *Type:* | string |
| *Default values:* | None |
| *Example value:* | '`uuidgen`' |
