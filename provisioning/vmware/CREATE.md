# Create PNDA cluster

![](../images/breadcrumbs-create.jpg)

## Introduction

With the target platform fully prepared, the PNDA software staged and the YAML configuration completed, the final step is to invoke the PNDA CLI to create the cluster.

## Create PNDA

#### Install CLI dependencies

To use the PNDA command line interface, you will need to install the python and terraform clients. To install them on CentOS, run:

```
# Download terraform and place on $PATH
cd /tmp
curl -LOJ https://releases.hashicorp.com/terraform/0.11.7/terraform_0.11.7_linux_amd64.zip
unzip terraform_0.11.7_linux_amd64.zip
sudo cp terraform /usr/local/bin/

sudo yum install -y epel-release
sudo yum install -y python python-pip python-devel gcc

cd cli
sudo pip install -r requirements.txt
```

#### CLI invocation

The pnda-cli.py script allows launching a PNDA deployment. It sits in the cli subdirectory.

```
cd cli
./pnda-cli.py
usage: pnda-cli.py [-h] [-e PNDA_CLUSTER] [-n DATANODES] [-o OPENTSDB_NODES]
                   [-k KAFKA_NODES] [-z ZK_NODES] [-f FLAVOR] [-s KEYNAME]
                   [-x] [-b BRANCH] [-d] [-m X_MACHINES_DEFINITION] [-v]
                   {create,expand,destroy}
```

| Options | Description |
| --- | --- |
| -e | Namespaced environment for machines in this cluster.
| -n | How many datanodes needed for the hadoop cluster
| -o | How many Open TSDB nodes for the hadoop cluster
| -k | How many kafka nodes for the databus cluster
| -z | How many zookeeper nodes for the databus cluster
| -f | PNDA flavours: ['pico', 'standard']
| -s | Name of key-pair name created in the OpenStack Tenant during the preparation phase.
| -b | Branch of [platform-salt](https://github.com/pndaproject/platform-salt) to use. Overrides value in pnda_env.yaml
| -v | Verbose logging

Note: Make sure you have access to the private key of this key pair otherwise you will not be able to connect to the gateway node and access the cluster.

Examples on invocation of CLI:

##### Create new cluster:
```
cd cli
pnda-cli.py create -e <cluster_name> -s <key_name> -f standard -o 2 -n 3 -k 2 -z 3 -b release/4.0
```
The options shown select the standard flavor, 2 openTSDB instances, 3 hadoop datanodes, 2 kafka brokers, and 3 zookeeper nodes. If you need to operate within the Openstack tenant instance quota of 20 instances then you can reduce this to 1 kafka and 1 zookeeper instance or use the pico flavor.

```
pnda-cli.py create -e <cluster_name> -s <key_name> -f standard -o 1 -n 1 -k 1 -z 1 -b release/4.0
pnda-cli.py create -e <cluster_name> -s <key_name> -f pico -n 1 -k 1 -b release/4.0
```

##### Destroy existing cluster:
```
pnda-cli.py destroy -e <cluster_name>
```
##### Expand existing cluster:
```
pnda-cli.py expand -e <existing_cluster_name> -f standard -s <key_name> -n 10 -k 5
```
Either, or both, kafka (k) and datanodes (n) can be changed.
        The value specifies the new total number of nodes.
        Shrinking is not supported - this must be done very carefully to avoid data loss.

To orchestrate PNDA on Openstack clone [pnda-cli repository](https://github.com/pndaproject/pnda-cli).

**Important:** ensure you are certain what version of PNDA you want to deploy, and specify the correct branch or tag when invoking the CLI using the -b option. In most circumstances you'll want to make sure the branch or tag you specify is identical to the branch or tag you used to build the PNDA mirror, and identical to the version you checked out from the pnda-cli repository. All PNDA releases are designated with a tag such as ```release/4.0``` across all repositories.

# [Home](../OVERVIEW.md)

| [Home](../OVERVIEW.md) | [Prepare](PREPARE.md) | [Image](IMAGE.md) | [Configure](CONFIGURE.md) | [Create](CREATE.md) | 
| --- | --- | --- | --- | --- | 
