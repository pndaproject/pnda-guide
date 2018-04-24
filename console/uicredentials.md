# UIs In PNDA

PNDA pulls together many different open source technologies, several of which provide a UI. To help with finding all these UIs the PNDA console links to the most useful ones, for example:

 - YARN resource manager
 - Hue
 - Jupyter
 - Grafana
 - PNDA Logserver
 - Flink

The cog icons on the PNDA console home page link to the UI for that component.

The PNDA console "metrics" page also contains a list of links to various UIs.

## Default Credentials
 - (CDH only) Cloudera Manager: admin/admin
 - (HDP only) Ambari: admin/admin
 - Grafana: pnda/pndapnda
 - Jupyter:
    - dev1/dev1, prod1/prod1 (when using the default pam_module: 'pam_unix' authentication) 
 - Everything else:  pnda/pnda

## Setting Credentials
If different passwords are required they can be configured in the platform-salt configuration before provisioning PNDA:
 - The Cloudera Manager credentials are located in [platform-salt/pillar/services.sls:admin_login](https://github.com/pndaproject/platform-salt/blob/develop/pillar/services.sls)
 - The PNDA user credentials are located in [platform-salt/pillar/pnda.sls:pnda](https://github.com/pndaproject/platform-salt/blob/develop/pillar/pnda.sls)
 - The Jupyter user credentials (when using  the default pam_module: 'pam_unix' authentication) are located in [platform-salt/pillar/identity.sls](https://github.com/pndaproject/platform-salt/blob/develop/pillar/identity.sls)

Note that for the PNDA user `password_hash` should be set along with the `user` and `password`. The easiest and most reliable way to do this is to set the password on a RHEL 7 machine, then look in /etc/shadow for the password hash.

The credentials can also be changed manually in each UI as required after PNDA has been provisioned.

Note that if the Cloudera Manager or Ambari password is changed, then the following salt states should be rerun, as these components require access to the Cloudera Manager API:
 - [platform-testing](https://github.com/pndaproject/platform-salt/tree/develop/salt/platform-testing)
 - [deployment-manager](https://github.com/pndaproject/platform-salt/tree/develop/salt/deployment-manager)
 - [data-service](https://github.com/pndaproject/platform-salt/tree/develop/salt/data-service)
 - [hdfs-cleaner](https://github.com/pndaproject/platform-salt/tree/develop/salt/hdfs-cleaner)
 - [jupyter](https://github.com/pndaproject/platform-salt/tree/develop/salt/jupyter)
 - [opentsdb](https://github.com/pndaproject/platform-salt/tree/develop/salt/opentsdb)
