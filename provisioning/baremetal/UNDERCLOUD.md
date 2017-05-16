# Setting up Undercloud

![](../images/breadcrumbs.jpg)

## Introduction

The Undercloud is the infrastructure director node. It is a single-system OpenStack installation that includes components for provisioning and managing the servers that ultimately form the underlying PNDA cluster. 

The principal role of the Undercloud is bare metal system control -  via Ironic it uses the Intelligent Platform Management Interface (IPMI) of each node for power management control and a PXE-based service to discover hardware attributes and install software to each node. This provides a method by which we can provision bare metal systems as if they were regular OpenStack nodes.

Please see [the TripleO documentation](http://tripleo.org/) for more information on the architectural concepts.

At part of the Undercloud build we will also create the necessary images for both discovering and provisioning the bare metal servers and for the PNDA nodes.

This VM will contain a single-system OpenStack installation that includes components for provisioning and managing the rest of the cluster.

To set up the Undercloud we need to carry out the following steps -

- Create virtual network for the VM
- Prepare a disk image for the VM
- Create the VM
- Configure the VM networking

## Walkthrough

These instructions are carried out on the Build Node.

##### Setup a user that has sudo privildges

The user performing all of the setup steps needs to have sudo enabled. 

```
sudo useradd stack
sudo sh -c 'echo "cisco" | passwd stack --stdin'
echo "stack ALL=(root) NOPASSWD:ALL" | sudo tee -a /etc/sudoers.d/stack
sudo chmod 0440 /etc/sudoers.d/stack
su - stack
```

##### Install libvirtd/kvm hypervisor services
```	
sudo yum -y install libvirt qemu-kvm virt-manager virt-install libguestfs-tools qemu-kvm-tools
sudo systemctl enable libvirtd
sudo systemctl start libvirtd
```

#### Create the provisioning network

We use virsh to create a network called 'provisioning' with a defined address range.

```
cat > /tmp/provisioning.xml <<EOF
<network>
  <name>provisioning</name>
  <ip address="172.16.0.254" netmask="255.255.255.0"/>
</network>
EOF

sudo virsh net-define /tmp/provisioning.xml
sudo virsh net-autostart provisioning
sudo virsh net-start provisioning
```

#### Prepare the undercloud disk image

We use a generic CentOS image and call it ```undercloud.qcow2```. 

```
wget http://cloud.centos.org/centos/7/images/CentOS-7-x86_64-GenericCloud.qcow2.xz
xz -d CentOS-7-x86_64-GenericCloud.qcow2.xz
sudo cp CentOS-7-x86_64-GenericCloud.qcow2 /var/lib/libvirt/images/undercloud.qcow2
```

#### Resize the undercloud disk

The generic Centos image does not have sufficient disk space. Resize it now.

```
sudo qemu-img resize /var/lib/libvirt/images/undercloud.qcow2 +120G
```

#### Adjust the Undercloud root partition accordingly

The partition table then needs adjusting to make use of the additional space.

**TODO** For me the first step throws a 'Device or resource busy' error after successfully writing the partition - assume this is safely ignored? 

```
sudo virt-customize -a /var/lib/libvirt/images/undercloud.qcow2 --run-command 'echo -e "d\nn\n\n\n\n\nw\n" | fdisk /dev/sda'
sudo virt-customize -a /var/lib/libvirt/images/undercloud.qcow2 --run-command 'xfs_growfs /'
sudo virt-filesystems --long -h --all -a /var/lib/libvirt/images/undercloud.qcow2
```

#### Uninstall cloud-init as it is not required by the Undercloud

Cloud-init is installed in images designed to be used in the cloud. It's not needed here and will slow down the boot process as it tries to contact resources which don't exist.

```
sudo virt-customize -a /var/lib/libvirt/images/undercloud.qcow2 --run-command 'yum remove cloud-init* -y'
```

#### Set the root password for the Undercloud
```
sudo virt-customize -a /var/lib/libvirt/images/undercloud.qcow2 --root-password password:cisco
```

#### Enabling the ssh daemon
```
sudo virt-customize -a /var/lib/libvirt/images/undercloud.qcow2 --run-command 'systemctl enable sshd'
```

#### Customizing Undercloud interfaces

Add a second interface for the provisioning network 

```
sudo virt-customize -a /var/lib/libvirt/images/undercloud.qcow2 --run-command 'cp /etc/sysconfig/network-scripts/ifcfg-eth{0,1} && sed -i s/DEVICE=.*/DEVICE=eth1/g /etc/sysconfig/network-scripts/ifcfg-eth1'
sudo virt-customize -a /var/lib/libvirt/images/undercloud.qcow2 --run-command 'sed -i s/BOOTPROTO=.*/BOOTPROTO=none/g /etc/sysconfig/network-scripts/ifcfg-eth1'
```

#### Creating the Undercloud VM

```
sudo virt-install --ram 32768 --vcpus 4 --os-variant rhel7     --disk path=/var/lib/libvirt/images/undercloud.qcow2,device=disk,bus=virtio,format=qcow2     --import --noautoconsole --graphics vnc,listen=0.0.0.0,password=cisco --network network:default --network network:provisioning  --name undercloud
sudo virsh list --all
```

#### Place the host provisioning interface in the right bridge

**TODO** - why?

Determine the bridge name:

```
sudo virsh net-dumpxml provisioning | grep bridge
  <bridge name='virbr1' stp='on' delay='0'/>
```

Determine the if name. There should be two interfaces named `en*`, one will already have an IP address e.g. `inet 10.60.19.29/24`, and one will not, choose the one that *does not* aleady have an IP address.

```
ip a
2: enp7s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq master virbr1 state UP qlen 1000
    link/ether 00:25:b5:42:00:f4 brd ff:ff:ff:ff:ff:ff
    inet6 fe80::225:b5ff:fe42:f4/64 scope link
       valid_lft forever preferred_lft forever
3: enp8s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP qlen 1000
    link/ether 00:25:b5:42:00:f2 brd ff:ff:ff:ff:ff:ff
    inet 10.60.19.29/24 brd 10.60.19.255 scope global dynamic enp8s0
       valid_lft 319202sec preferred_lft 319202sec
    inet6 2001:420:44f1:3:225:b5ff:fe42:f2/64 scope global noprefixroute dynamic
       valid_lft 2591962sec preferred_lft 604762sec
    inet6 fe80::225:b5ff:fe42:f2/64 scope link
       valid_lft forever preferred_lft forever
```

```
sudo brctl addif virbr1 enp7s0
```

# Deploying the Undercloud on the VM

## Overview

## Walkthrough

After determining the IP address of the Undercloud VM and connecting, the remainder of these instructions are carried out on the Undercloud VM.

#### Retrieve the undercloud VM ip address
```
sudo virsh domifaddr undercloud
```

#### Log into the undercloud VM using the above retrieved ip address
```
ssh root@<undercloud ip address>
cisco
```

### Preliminary configuration

#### Creating the stack user

```
sudo useradd stack
echo "cisco" | passwd stack --stdin
echo "stack ALL=(root) NOPASSWD:ALL" | sudo tee -a /etc/sudoers.d/stack
sudo chmod 0440 /etc/sudoers.d/stack
su - stack
```
#### Setting the host name, replace the ```<undercloud_fqdn>``` with the appropriate hostname
```
sudo hostnamectl set-hostname <undercloud_fqdn>
sudo hostnamectl set-hostname --transient <undercloud_fqdn>
```

for example:

```
sudo hostnamectl set-hostname undercloud
sudo hostnamectl set-hostname --transient undercloud
```

#### Updating hosts file

```
sudo vi /etc/hosts
```

```
127.0.0.1   <undercloud_hostname> <undercloud_fqdn> localhost
```

for example:

```
127.0.0.1   undercloud undercloud localhost
```

#### Setting up the required repositories

DLRN (Delorean) builds and maintains yum repositories following OpenStack uptream commit streams.

Documentation is available at http://dlrn.readthedocs.org/en/latest/

```
sudo curl -L -o /etc/yum.repos.d/delorean-mitaka.repo https://trunk.rdoproject.org/centos7-mitaka/current/delorean.repo
sudo curl -L -o /etc/yum.repos.d/delorean-deps-mitaka.repo http://trunk.rdoproject.org/centos7-mitaka/delorean-deps.repo
sudo yum -y install --enablerepo=extras centos-release-ceph-jewel
sudo sed -i -e 's%gpgcheck=.*%gpgcheck=0%' /etc/yum.repos.d/CentOS-Ceph-Jewel.repo
```

#### Installing the TripleO client

TripleO is a project aimed at installing, upgrading and operating OpenStack clouds using OpenStack’s own cloud facilities as the foundation - building on Nova, Ironic, Neutron and Heat.

Documentation is available at https://docs.openstack.org/developer/tripleo-docs/

```
sudo yum -y install yum-plugin-priorities
sudo yum install -y python-tripleoclient
```

#### Undercloud configuration

```
cat > ~/undercloud.conf <<EOF
[DEFAULT]
undercloud_hostname = <undercloud_fqdn>
local_ip = 192.0.3.1/24
network_gateway = 192.0.3.1
undercloud_public_vip = 192.0.3.2
undercloud_admin_vip = 192.0.3.3
local_interface = <provisioning_interface>
network_cidr = 192.0.3.0/24
masquerade_network = 192.0.3.0/24
dhcp_start = 192.0.3.5
dhcp_end = 192.0.3.24
inspection_interface = br-ctlplane
inspection_iprange = 192.0.3.100,192.0.3.120
inspection_runbench = false
undercloud_debug = true
enable_mistral = false
enable_zaqar = false
ipxe_deploy = false
enable_monitoring = false
store_events = false
[auth]
EOF
```
Edit the ```undercloud.conf``` file to replace the ```<undercloud_fqdn>``` and the ```<provisioning_interface>``` fields. The provisioning interface is the one without an IP address, ```ip a``` can be used to figure out which one.
```
ip a
vi ~/undercloud.conf
```

#### Undercloud deployment

This step can take some time to complete. This is normal.

```
export DIB_YUM_REPO_CONF=`ls /etc/yum.repos.d/*.repo`
export NODE_DIST=centos7
openstack undercloud install

[…]

#############################################################################`
Undercloud install complete.

The file containing this installation's passwords is at
/home/stack/undercloud-passwords.conf.

There is also a stackrc file at /home/stack/stackrc.

These files are needed to interact with the OpenStack services, and should be
secured.

#############################################################################
```
From now on, one can authenticate using the credentials from the ```stackrc``` file
```
. stackrc
openstack service list
+----------------------------------+------------+---------------+
| ID                               | Name       | Type          |
+----------------------------------+------------+---------------+
| 0216b22ec85b437d9cc80b3a34ff2da6 | ceilometer | metering      |
| 16b7cfa2f8f64484902c8d7c7c832597 | neutron    | network       |
| 228290231a9d48f09b6f801641a773c8 | glance     | image         |
| 3493e5ad8f3e4e14a9f91dc10849540d | novav3     | computev3     |
| 56cce03fd1b74a8c833650f8fe83639a | heat       | orchestration |
| 8a8be34a81e74aa5ad522c20c447d647 | nova       | compute       |
| a97775a728344d3db3fa655ba39bd16d | keystone   | identity      |
| a9f7e037e49b4e068015263e99d0ae21 | ironic     | baremetal     |
| bc0aaf1a1de941018eff9637c879a2ca | swift      | object-store  |
+----------------------------------+------------+---------------+
```

#### Setting up a working upstream DNS server

Select a suitable upstream DNS server in your environment and substitute it into the command below.

```
neutron subnet-list
+--------------------------------------+------+--------------+---------------------------------------------+
| id                                   | name | cidr         | allocation_pools                            |
+--------------------------------------+------+--------------+---------------------------------------------+
| d232fcfe-38e1-4ff7-8258-90830fd716b8 |      | 192.0.3.0/24 | {"start": "192.0.3.5", "end": "192.0.3.24"} |
+--------------------------------------+------+--------------+---------------------------------------------+
neutron subnet-update --dns-nameserver <upstream DNS server> d232fcfe-38e1-4ff7-8258-90830fd716b8
```

### Building the overcloud images

There are two types of images:
*	Deployment and discovery images - these are standard images from upstream projects
*	The PNDA image - this is bespoke for PNDA

#### Building deployment and discovery images

##### WARNING 

**As of 2017-02-20 this step doesn't seem to work. See https://bugs.launchpad.net/tripleo/+bug/1666189**

```
export DIB_YUM_REPO_CONF=`ls /etc/yum.repos.d/*.repo`
export NODE_DIST=centos7
mkdir ~/images && cd ~/images
openstack overcloud image build --all
```

If this doesn't work, try downloading and untarring the images to the ~/images directory manually.

```
mkdir ~/images && cd ~/images
curl -LO https://buildlogs.centos.org/centos/7/cloud/x86_64/tripleo_images/mitaka/delorean/ironic-python-agent.tar
curl -LO https://buildlogs.centos.org/centos/7/cloud/x86_64/tripleo_images/mitaka/delorean/overcloud-full.tar
curl -LO https://buildlogs.centos.org/centos/7/cloud/x86_64/tripleo_images/mitaka/delorean/undercloud.qcow2
tar xf ironic-python-agent.tar
tar xf overcloud-full.tar
```

#### Swift proxy

Reconfigure swift proxy server to handle large objects.

	sudo yum install -y openstack-utils
	sudo openstack-config --set /etc/swift/proxy-server.conf DEFAULT client_timeout 120
	sudo openstack-config --set /etc/swift/proxy-server.conf DEFAULT node_timeout 120
	sudo openstack-service restart swift

#### Building the pnda image

The PNDA base image is created with the [pnda-dib-elements](https://github.com/pndaproject/pnda-dib-elements) project.

Clone the project and follow the instructions in the README.md file for CentOS

```
cd
git clone https://github.com/pndaproject/pnda-dib-elements.git

```

Then follow the [README.md](https://github.com/pndaproject/pnda-dib-elements/blob/develop/README.md) instructions

When the image is ready,

```
cp -v pnda-image.qcow2 ~/images
```

#### Uploading the previously created/customized images into the undercloud's glance.
```
cd ~/images
openstack overcloud image upload
glance image-create  --name pnda-image --disk-format qcow2 --container-format bare --file pnda-image.qcow2 --progress
```

#[Next](SALTMASTER.md)

| [Home](../OVERVIEW.md) | [Prepare](PREPARE.md) | [Undercloud](UNDERCLOUD.md) | [Saltmaster](SALTMASTER.md) | [Register](REGISTER.md) | [Mirror](MIRROR.md) | [Build](BUILD.md) | [Stage](STAGE.md) | [Configure](CONFIGURE.md) | [Create](CREATE.md) | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

