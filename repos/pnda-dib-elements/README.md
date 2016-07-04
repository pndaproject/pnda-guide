# PNDA DIB Elements

Deploying PNDA using Heat templates requires an image with some pre-installed elements, such as `os-collect-config`. This guide describes how to build such an image from a set of external disk-image-builder elements.

PNDA currently uses the Ubuntu operating system. 

## Pre-requesites

**Important:** these dependencies install correctly on an Ubuntu 14.04 *server* image but fail on a *desktop* images.

```
sudo apt-get -y install python-pip python-dev qemu-utils libguestfs-tools
```

## Setting up a virtualenv

Install virtualenv:

```
pip install --user virtualenv
```

Create the virtual environment:

```
virtualenv /path/to/project/pnda-dib
. /path/to/project/pnda-dib/bin/activate
```

## Getting the required elements

Clone the `openstack/tripleo-image-elements` repo:

```
git clone https://github.com/openstack/tripleo-image-elements.git
```

Clone the `openstack/heat-templates` repo:

```
git clone https://github.com/openstack/heat-templates.git
```

Install `openstack/diskimage-builder`:

```
git clone https://github.com/openstack/dib-utils.git
cd dib-utils
python setup.py install
cd ..
git clone https://github.com/openstack/diskimage-builder.git
cd diskimage-builder
python setup.py install
cd ..
pip install six
pip install PyYAML
```

Set up environment variables, assuming you currently are in this repository's project directory (there is at least a pnda-cloud-init directory present):


```
cat > dib_env.sh <<EOF
export ELEMENTS_PATH=tripleo-image-elements/elements:heat-templates/hot/software-config/elements:.
export BASE_ELEMENTS="ubuntu"
# MANDATORY ELEMENTS FOR PNDA PROVISIONING
export AGENT_ELEMENTS="os-collect-config os-refresh-config os-apply-config"
# MANDATORY ELEMENTS FOR PNDA PROVISIONING
export DEPLOYMENT_BASE_ELEMENTS="heat-config heat-config-script"
# NON MANDATORY ELEMENTS FOR PNDA PROVISIONING
# but might be helpful if you plan to use anible, saltstack or puppet
# export DEPLOYMENT_TOOL="heat-config-ansible heat-config-salt heat-config-puppet"
# PNDA ELEMENTS
export PNDA_ELEMENTS="cloud-init-pnda"
export IMAGE_NAME=ubuntu-software-config
export ALL_ELEMENTS="\$BASE_ELEMENTS \$AGENT_ELEMENTS \$DEPLOYMENT_BASE_ELEMENTS \$DEPLOYMENT_TOOL \$PNDA_ELEMENTS"
EOF
. dib_env.sh
```

## Build the image

```
disk-image-create vm $ALL_ELEMENTS -o $IMAGE_NAME.qcow2
```

## Upload the image to the OpenStack infrastructure

Install the glance client:

```
pip install python-glanceclient
```

Upload the image to the OpenStack image service:

```
. your_openstack_rc.sh
glance image-create --name pnda-base --file ubuntu-software-config.qcow2 --progress --disk-format qcow2 --container-format bare --is-public true
```
