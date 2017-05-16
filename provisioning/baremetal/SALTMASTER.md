# Setting up Saltmaster VM

![](../images/breadcrumbs.jpg)

## Introduction

PNDA uses SaltStack to take care of provisioning, managing configuration and upgrading at the software and services layer above the infrastructure. Please see [this quick overview of SaltStack](https://docs.saltstack.com/en/latest/topics/tutorials/walkthrough.html).

SaltStack servers are either designated Master and Minion. The Master is the server hosts all policies and configuration and pushes those to the minions. The Minions are the infrastructure hosts that are to be managed. All communication is encrypted and Minions are securely authenticated with the Master.

This VM will be the SaltStack Master node and host all policies and configuration and coordinate the way these are communicated to to the SaltStack Minions.

## Walkthrough

These instructions are carried out on the Build Node.

#### Creating the VM

```
sudo qemu-img create -f qcow2 -o preallocation=metadata /var/lib/libvirt/images/pnda-master.qcow2 40G
sudo virt-install  --name=pnda-master --file=/var/lib/libvirt/images/pnda-master.qcow2 --graphics vnc,listen=0.0.0.0 --vcpus=1 --ram=4096 --network network=provisioning,model=virtio --os-type=linux --boot hd --dry-run --print-xml > pnda-master.xml
sudo virsh define pnda-master.xml
sudo virsh domiflist pnda-master
```
Remember the instance's mac address

# [Next](REGISTER.md)

| [Home](../OVERVIEW.md) | [Prepare](PREPARE.md) | [Undercloud](UNDERCLOUD.md) | [Saltmaster](SALTMASTER.md) | [Register](REGISTER.md) | [Mirror](MIRROR.md) | [Build](BUILD.md) | [Stage](STAGE.md) | [Configure](CONFIGURE.md) | [Create](CREATE.md) | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
