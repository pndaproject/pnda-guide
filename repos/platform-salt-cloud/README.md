# Using Salt Cloud #

These instructions assume that you have already configured a Salt Master node in your cloud environment.

## Installation ##

Salt-cloud is installed with SaltStack. Please refer to the [Salt Cloud installation instructions](https://docs.saltstack.com/en/latest/topics/cloud/install/index.html).

## Configuration ##

Copy `etc/salt` to `/etc/salt` of the Salt Master.

Edit `/etc/salt/cloud.providers.d/cloud_provider.conf` and set the variables that the template requires.

You should now be able to list providers:


    $ sudo salt-cloud --list-providers
    [INFO    ] salt-cloud starting
    mycloud:
        ----------
        openstack:
            ----------

## Launch one instance ##

You can now launch one instance named `test` with the profile named `ubuntu`.

    $ sudo salt-cloud -p ubuntu test


## Using map files ##

On the Salt Master -

Creating the instances

    $ sudo salt-cloud -m map/demo.map -P

Deleting the instances

    $ sudo salt-cloud -m map/demo.map -d
