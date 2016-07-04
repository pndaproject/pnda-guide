# Salt Master

To setup salt master (change address):

Requirements:

```sh
sudo apt-get update
sudo apt-get -y install python-pip python-git
sudo pip install netaddr apache-libcloud
```

Then download salt installer and install it

```sh
wget -O install_salt.sh https://bootstrap.saltstack.com
sudo sh install_salt.sh -D -U -M stable 2015.8.10
```

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

Then save and restart salt master process

```sh
sudo restart salt-master
```

In order for the salt/cloud events to work, you need to configure salt-cloud inside salt. It means, having `/etc/salt/cloud` and `/etc/salt/cloud.{providers,profiles}.d` configuration files.

## Git repo for Salt recipes

On the saltmaster instance, setup git repos (need to be done once and only once):

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

## Git repo initialization

For the following git repos from the pnda git server:

- platform-salt
- platform-salt-cloud

On a developer workstation, after cloning the said repos, perform the following steps where `saltmaster` refers to the remote saltmaster server:



```
cd <local-path>/platform-salt
```
```
git remote add saltmaster ssh://cloud-user@saltmaster/home/cloud-user/git_repos/saltmaster.git
```
```
cd <local-path>/platform-salt-cloud
```
```
git remote add saltmaster ssh://cloud-user@saltmaster/home/cloud-user/git_repos/salt-cloud.git
```

So that once making updates, you will need to put them both on the pnda git server and the saltmaster git remotes

```sh
git push saltmaster master
```

## Miscellaneous (salt-master)

Setup access to repo from local (need to be done once)

```sh
cd /home/cloud-user/johndoe
git clone file:///home/saltgit/saltmaster.git
```
