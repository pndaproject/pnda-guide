# Create PNDA cluster

![](../images/breadcrumbs-create.jpg)

## Introduction

With the target platform fully prepared, the PNDA software staged and the YAML configuration completed, the final step is to invoke the PNDA CLI to create the cluster.

Underneath, Nova will select suitable machines via Ironic based on the earlier flavor tagging and the machines will be PXE booted and images installed on the physical disks. 

## Create PNDA

#### Install CLI dependencies

To use the PNDA command line interface, you will need to install the python, heat and nova clients. To install them on CentOS, run:

```
sudo yum install -y epel-release
sudo yum install -y python python-pip python-devel

cd cli
sudo pip install -r requirements.txt
```

#### Keystone authentication

You must authenticate with Keystone before using the OpenStack clients.

The easiest way to accomplish this is to download the credentials file usually named <project>-openrc.sh from Horizon for the given user and tenant, then source it in the shell you will use to create PNDA.

```
. <project>-openrc.sh
```

#### CLI invocation

The heat_cli.py scripts allows to launch a PNDA deployment. It sits in the cli subdirectory.


```
cd cli
./heat_cli.py
usage: heat_cli.py [-h] [-y] [-e PNDA_CLUSTER] [-n DATANODES]
                   [-o OPENTSDB_NODES] [-k KAFKA_NODES] [-z ZK_NODES]
                   [-f {standard}] [-b BRANCH] [-s KEYPAIR]
                   {create,destroy}
```

In particular note that the -s option refers to the key pair created in the OpenStack tenant during the preparation phase.

Make sure you have access to the private key of this key pair otherwise you will not be able to connect to the bastion node and access the cluster.

```
./heat_cli.py -e pnda-cluster -f bmstandard -b master -s <key pair> -bare true -fstype local create
```

#### Updating the undercloud ```/etc/hosts``` file
```
openstack server list -c Networks -c Name | grep -v Networks|awk {'print $4,$2'}|cut -d\= -f2 - |sudo tee -a /etc/hosts
```

#### Connecting to PNDA

Forward host port 2222 to the undercloud ssh port
```
iptables -t nat -I PREROUTING -p tcp -d 10.60.19.29 --dport 2222 -j DNAT --to-destination 192.168.122.73:22
iptables -I FORWARD -m state -d 192.168.122.0/24 --state NEW,RELATED,ESTABLISHED -j ACCEPT
```

# [Home](../OVERVIEW.md)

| [Home](../OVERVIEW.md) | [Prepare](PREPARE.md) | [Undercloud](UNDERCLOUD.md) | [Saltmaster](SALTMASTER.md) | [Register](REGISTER.md) | [Mirror](MIRROR.md) | [Build](BUILD.md) | [Stage](STAGE.md) | [Configure](CONFIGURE.md) | [Create](CREATE.md) | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
