# Configuring a Salt master

If provisioning with Salt Cloud then a Salt Master must be created in the cloud environment.

## Instance requirements

The Salt Master can run on a modestly sized instance. For example 2 vCPUs, 8 GB memory and 50 GB disk is adequate.

To setup Salt Master follow these steps. For the purpose of this guide we'll assume that:

* The Salt Master is to be installed on a host called "saltmaster"
* A user in the sudoers list on Salt Master is "cloud-user"

## Requirements

Install required software:

```sh
sudo apt-get update
sudo apt-get -y install python-pip python-git
sudo pip install netaddr apache-libcloud
```

Then download the Salt installer and install Salt Master.

```sh
wget -O install_salt.sh https://bootstrap.saltstack.com
sudo sh install_salt.sh -D -U -M stable 2015.8.10
```

As the bootstrap cloud did not install salt-cloud, you will need to install it manually, for doing so, please check [SaltStack documentation on salt-cloud](https://docs.saltstack.com/en/latest/topics/cloud/#quickstart). 

Replace /etc/salt/master with the following content:

```yaml
## specific PNDA saltmaster config
auto_accept: True      # auto accept minion key on new minion provisioning

## Using GitFS backend
fileserver_backend:
  - git
  - minion

gitfs_remotes:
  - file:///home/cloud-user/git_repos/saltmaster.git:
    - root: salt

gitfs_base: master

# Do not merge top.sls files across multiple environments
top_file_merging_strategy: same

ext_pillar:
   - git: __env__ file:///home/cloud-user/git_repos/saltmaster.git root=pillar

# To autoload new created modules, states add and remove salt keys,
# update bastion /etc/hosts file automatically ... add the following reactor configuration
reactor:
  - 'minion_start':
    - salt://reactor/sync_all.sls
  - 'salt/cloud/*/created':
    - salt://reactor/create_bastion_host_entry.sls
  - 'salt/cloud/*/destroying':
    - salt://reactor/delete_bastion_host_entry.sls

file_recv: True

failhard: True

## end of specific PNDA saltmaster config
```

Then save and restart salt master process.

```sh
sudo restart salt-master
```


## Git repository for Salt recipes

**On the Salt Master instance**, set up git (need to be done once and only once):

```sh
mkdir /home/cloud-user/git_repos
cd /home/cloud-user/git_repos
for r in salt-cloud.git saltmaster.git; do
  mkdir $r
  cd $r
  git --bare init
  cd -
done
```

## Git repository initialization

**On a developer workstation**, git clone these PNDA repositories:

* platform-salt
* platform-salt-cloud

Then configure a remote for each repository so that you can push to the newly configured Salt Master.

```sh
cd <local-path>/platform-salt
git remote add saltmaster ssh://cloud-user@saltmaster/home/cloud-user/git_repos/saltmaster.git
cd <local-path>/platform-salt-cloud
git remote add saltmaster ssh://cloud-user@saltmaster/home/cloud-user/git_repos/salt-cloud.git

```
Finally, push to the Salt Master.

```sh
git push saltmaster master
```

## Configure Salt Cloud for provisioning

To configure salt-cloud for use **on the Salt Master instance**:

* Copy the content of etc/salt from the platform-salt repository to /etc/salt.
* Modify the content of /etc/salt/cloud.providers.d/cloud_provider.conf for your cloud environment.

## Configure Salt for provisioning

A Salt provisioning is configured by creating Salt pillar files in the `/srv/pillar` directory.
These files must be referenced by the `/srv/pillar/top.sls` file. For example:

/srv/pillar/top.sls

```yaml
{{ env }}:
  '*':
    - openstack
    - java
    - pnda
```

### Mandatory configuration

Create for the following files for Salt **on the Salt Master instance**:

/srv/pillar/openstack.sls

This MUST contains credentials for connecting with OpenStack.

```
keystone.user: <username>
keystone.password: <openstack api key>
keystone.tenant: <tenant name>
keystone.auth_url: <auth url>
```

/srv/pillar/pnda.sls

This MUST contains at least the [package server](../repos/pnda-package-server-docker/README.md) URI. For example:

```yaml
packages_server:
  base_uri: 'http://<private-packages-server>'
```

### Optional configuration

You can optionally add other pillar configuration to fit you needs. For example you can set an alternate location to download Oracle JDK by creating the following file:

/srv/pillar/java.sls

This provides source urls for downloading the JDK in case the Oracle mirror proves unreliable.

```yaml
java:
  source_url: 'http://<your-private-mirror>/java/jdk/8u74-b02/jdk-8u74-linux-x64.tar.gz'
```

## Usage

In order to make use of the PNDA CLI and Salt Cloud, clone the salt-cloud repository.

For example:

```sh
cd /home/cloud-user/johndoe
git clone file:///home/cloud-user/git_repos/salt-cloud.git
```
