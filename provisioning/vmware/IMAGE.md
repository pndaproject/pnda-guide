# Select & Prepare Platform

![](../images/breadcrumbs.jpg)

## Creating PNDA images

Deploying PNDA using Terraform requires images with some pre-installed elements, such as `VMWare tools`. 

PNDA currently runs on CentOS/RHEL, but you can use Ubuntu/CentOS/RHEL/Mac/Windows OSes to create the PNDA images.

### Pre-requisites

For building based images on VMWare, we are using [Packer](https://www.packer.io/) so make sure you've got the right package for your OS download and installed as describe [here](https://www.packer.io/downloads.html)


### Create PNDA images

All the process for building base images is describe on the [PNDA repo](https://github.com/pndaproject/pnda/blob/develop/packer/README.md)

```
git clone https://github.com/pndaproject/pnda.git
cd packer
```

Then follow the instructions describe in the README to build the differents images


# [Next](CONFIGURE.md)

| [Home](../OVERVIEW.md) | [Prepare](PREPARE.md) | [Image](IMAGE.md) | [Configure](CONFIGURE.md) | [Create](CREATE.md) | 
| --- | --- | --- | --- | --- | 
