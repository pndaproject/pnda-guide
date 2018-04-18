# Stage PNDA mirror & components

![](../images/breadcrumbs-stage.jpg)

## Introduction

In order to make built components available during the PNDA provisioning process, they need to be staged in a location that is accessible from the target environment via HTTP. 

## Creating PNDA Mirror VM

This VM will be the HTTP server from which PNDA will install the software it needs during the installation process. The software is built from our GitHub and simply copied to the relevant location.

## Walkthrough

#### Creating the VM

```
sudo qemu-img create -f qcow2 -o preallocation=metadata /var/lib/libvirt/images/appserver.qcow2 40G
sudo virt-install  --name=appserver --file=/var/lib/libvirt/images/appserver.qcow2 --graphics vnc,listen=0.0.0.0 --vcpus=1 --ram=4096 --network network=provisioning,model=virtio --os-type=linux --boot hd --dry-run --print-xml > appserver.xml
sudo virsh define appserver.xml
sudo virsh domiflist appserver
```
#### Retrieve its mac address
```
Interface  Type       Source     Model       MAC
-------------------------------------------------------
-          network    provisioning virtio      52:54:00:67:99:43
```
Create a json file to describe the app server machine (put the mac address above into it)
```
# cat ~/pnda-apps.json 
{
  "nodes": [
    {
      "arch": "x86_64",
      "disk": "10",
      "name": "pndaapps",
      "pm_addr": "192.168.122.1",
      "pm_password": "-----BEGIN RSA PRIVATE KEY-----\nMIICXQIBAAKBgQCygHMyAqkDzblq8MD9RP5FQbuAzB2GQdlwKxSvCBQEkqe9Y6Kf\n8iSQCfiuKB8uoAAHMQCZ6tu8QRbxmy71OhtvZU8cc8x9w2Nzcn5M+JVyMKhBRGZd\n7YUjCpqDeVasDjAzFf286BeZSeiPE7DEjAAfo957zrEMJJyoKJDSQeoI+QIDAQAB\nAoGBAIS69u2NBNiLNQDMHPU3REuDYUWYgau/c0vw/ORaAWiVFJ3DZL3CdGWWxI/b\nzbQBzYOLcIMDHHmTfNgTKIu4tYSUQaW7lwBTkjZSG80nVapatLT/RwJlmUQSyU8w\ndgAUml+Nq0iF+/FRAHRa6UvUpLY1ZfDrEsoQvqcnX/ghx8uxAkEA69pR8A1fAwY9\nuqyvpx6QTs8DhsIbGHfdk3o7ZFiKxrQ2k6R1MB5fIV5RrdfADuuGT4J0jruSELRD\nUvb6oD0dBwJBAMG/9vit7pjuOxh86lsi8rDJ1x0qi65DifIw+ffB7NwC84lUxZmm\nRaBeACYLPrSCddlD5LMG6V1NUb54adR8Kf8CQD0ag83weOQcstNxN9TRO0vfoCdC\nlKiDLXmu2kJGGjYerGEV43KC+9x2Ri0Gz3BOHq7sumvcNpxzR1nwOMBY9PMCQFDf\nrFuJXrr/VjOWkMyR/fPFjMFj7QJEtuQdhXnhvNjpcna0p/bG7PFPy4gV0YrPmhmi\nuWfxTp/fkmuLH8HOQkkCQQDYFxfYHDNf/I65lN5bocawrCxEJ6h4s/cbs3lzxX/z\nC56t9ikNEWmfQle8BOj5fbRi6r44YFXanZX+qEGe2RZd\n-----END RSA PRIVATE KEY-----",
      "pm_user": "root",
      "pm_type": "pxe_ssh",
      "mac": [
        "52:54:00:67:99:43"
      ],
      "cpu": "1",
      "memory": "1024"
    }
  ]
}
```
#### Add the machine to ironic
```
openstack baremetal import ~/pnda-apps.json
openstack baremetal configure boot
```
#### Check the machine existence
```
[root@undercloud ~]# openstack baremetal list |grep pndaapps
| 0e389292-e8f5-4653-ba04-658b5503ae86 | pndaapps      | None          | power on    | manageable         | True        |
```
#### Introspect the machine
```
ironic node-set-provision-state 0e389292-e8f5-4653-ba04-658b5503ae86 manage
ironic node-set-maintenance 0e389292-e8f5-4653-ba04-658b5503ae86 on
openstack baremetal introspection start 0e389292-e8f5-4653-ba04-658b5503ae86
```
#### Wait for introspection to finish
```
[root@undercloud ~]# openstack baremetal introspection status 0e389292-e8f5-4653-ba04-658b5503ae86
+----------+-------+
| Field    | Value |
+----------+-------+
| error    | None  |
| finished | True  |
+----------+-------+
```
#### Set the machine to a deployable state
```
ironic node-set-provision-state 0e389292-e8f5-4653-ba04-658b5503ae86 provide
ironic node-set-maintenance 0e389292-e8f5-4653-ba04-658b5503ae86 off
```
#### Create a flavor and tag everyone as appserver
```
openstack flavor create --id auto --ram 2048 --disk 10 --vcpus 1 appserver
openstack flavor set --property "cpu_arch"="x86_64" --property "capabilities:boot_option"="local" --property "capabilities:profile"="appserver" appserver
ironic node-update 0e389292-e8f5-4653-ba04-658b5503ae86 add properties/capabilities=profile:appserver,boot_option:local
```
#### Create an app server instance
```
openstack server create --flavor appserver --image pnda-image --key-name default appserver
openstack server list | grep appserver
| 02ac07fa-4816-4636-85b1-e8123412fd19 | appserver | BUILD  | ctlplane=192.0.3.6 |
```
#### Once the status is ACTIVE connect to the created server
```
ssh cloud-user@192.0.3.6
```
#### Install an http server
```
sudo apt -y install apache2
```

## Stage files

Copy the *contents* of ```mirror-dist``` and ```pnda-dist``` from the mirror creation and build steps respectively to the HTTP server.

The final directory layout should resemble the following -

```
pnda-root
│
├── console-backend-data-logger-develop.tar.gz
├── console-backend-data-logger-develop.tar.gz.sha512.txt
├── etc
│
├── mirror_anaconda
│   ├── Anaconda-4.0.0-el7.parcel
│   ├── etc
│
├── mirror_rpm
│   ├── a-rpm.rpm
│   ├── etc
│
├── etc
```

Note that ```pnda-root``` can be any location, all that is required is that the hierarchy under this is available via a known URI. For example, using a standard Apache 2 installation on RHEL, if the hierarchy above is placed in ```/var/www/html``` this will be available via the URI ```http://<server>/``` since ```/var/www/html``` is the default *document root*. Please refer to your HTTP server documentation for more details.

Make a note of the URI to ```pnda-root``` as this will be used in configuring the PNDA creation process.

# [Next](CONFIGURE.md)

| [Home](../OVERVIEW.md) | [Prepare](PREPARE.md) | [Undercloud](UNDERCLOUD.md) | [Saltmaster](SALTMASTER.md) | [Register](REGISTER.md) | [Mirror](MIRROR.md) | [Build](BUILD.md) | [Stage](STAGE.md) | [Configure](CONFIGURE.md) | [Create](CREATE.md) | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
