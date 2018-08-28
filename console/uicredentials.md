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
Most UI's use PAM authentication (which includes pam_unix for local user authentication and pam_ldap for LDAP user authentication).

### Default users

All the users that can be authenticated by the configured LDAP server will get access to the *user facing UI's*.

When LDAP configuration is missing, two default local users are present on the cluster:

| user  | password | group |
| ---   | ---      | ---
| dev1  | dev1     | dev |
| prod1 | prod1    | prod |

### Admin users

The default local admin user is:

| user  | password | group |
| ---   | ---      | ---
| pnda  | pnda     | pnda |

Due to limitations in some of some of the used open source technologies, some UI's can not authenticate through PAM and are connected directly with the LDAP server.

 - Hadoop Cluster Manager: admin/admin
 - Grafana: pnda/pndapnda

## Setting Credentials
If different passwords are required they can be configured in the platform-salt configuration before provisioning PNDA:
 - The Hadoop Cluster Manager admin user credentials are located in [platform-salt/pillar/services.sls:admin_login](https://github.com/pndaproject/platform-salt/blob/develop/pillar/services.sls)
 - The Grafana pnda user credentials are located in [platform-salt/salt/grafana/init.sls:grafana_pass](https://github.com/pndaproject/platform-salt/blob/develop/salt/grafana/init.sls)
 - The pnda user credentials are located in [platform-salt/pillar/pnda.sls:pnda](https://github.com/pndaproject/platform-salt/blob/develop/pillar/pnda.sls)
 - The default users (dev1/prod1) credentials (when no LDAP properties are configured) are located in [platform-salt/pillar/identity.sls](https://github.com/pndaproject/platform-salt/blob/develop/pillar/identity.sls)

Note that for the PNDA user `password_hash` should be set along with the `user` and `password`. The easiest and most reliable way to do this is to set the password on a RHEL 7 machine, then look in /etc/shadow for the password hash.

The credentials can also be changed manually in each UI as required after PNDA has been provisioned.

Note that if the Cloudera Manager or Ambari password is changed, then the following salt states should be rerun, as these components require access to the Cloudera Manager API:
 - [platform-testing](https://github.com/pndaproject/platform-salt/tree/develop/salt/platform-testing)
 - [deployment-manager](https://github.com/pndaproject/platform-salt/tree/develop/salt/deployment-manager)
 - [data-service](https://github.com/pndaproject/platform-salt/tree/develop/salt/data-service)
 - [hdfs-cleaner](https://github.com/pndaproject/platform-salt/tree/develop/salt/hdfs-cleaner)
 - [jupyter](https://github.com/pndaproject/platform-salt/tree/develop/salt/jupyter)
 - [opentsdb](https://github.com/pndaproject/platform-salt/tree/develop/salt/opentsdb)
