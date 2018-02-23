# Adding application dependencies

Applications have two distinct sets of dependency resolution considerations. Firstly, there is a driver process which runs on one of the edge nodes or on a data node, depending on how it was launched. Secondly, there are executor processes which run on data nodes.

## PNDA 'app-packages' ##

This is a mechanism for permanently installing Python dependencies into safe virtualenv sandboxes in designated locations around the PNDA cluster. Once installed, the dependencies can be referenced by applications from driver or executor proceses.

The dependencies are [python pip libraries](https://pypi.python.org/pypi) and are specified in the format required by [pip](https://pypi.python.org/pypi/pip). Refer to https://packaging.python.org/guides/tool-recommendations/ for more information.

Maintain this file:

```platform-salt/salt/app-packages/files/app-packages-requirements.txt```

Also ensure the PNDA mirror contains the corresponding packages.

On creation of PNDA a default set of libraries will be installed ready for use.

### Updates ###

If it becomes necessary to update the installed dependencies on PNDA, run this Salt State:

```sudo salt -C 'G@hadoop:role:EDGE or G@roles:jupyter or G@hadoop:role:DATANODE' state.sls app-packages```

## Usage ##

Use dependencies in the normal fashion, for example:

```import sharedroutines```

## Distributing at runtime ##

Sometimes it can be useful to distribute a dependency across the cluster only when an application is launched. For example, you may not want to permanently install a library on nearly every node on the cluster but only have it present where and when it's needed.

These dependencies usually take the form of Python [eggs](https://setuptools.readthedocs.io/en/latest/formats.html) but could also be JARs or zip archives.

In order for this approach to work the dependencies need to be accessible from anywhere on the cluster. Therefore, they are staged on the distributed file system.

Maintain this file:

```platform-salt/salt/app-packages/files/app-packages-hdfs.txt```

## Updates ##

Run this Salt State to stage the listed files at the HDFS path configured in the Pillar (see platform-salt/pillar/pnda.sls):

```sudo salt-call state.sls app-packages.hdfs-sync```

Also ensure the PNDA mirror contains the corresponding packages.

### Usage ###

In your PySpark environment, use the SparkContext.addPyFile method to add any required dependencies, referencing the full HDFS path configured as mentioned above. For example:

```sc.addPyFile('hdfs:///pnda/deployment/app_packages/sharedroutines-0.1.egg')```

## Mirror ##

All application dependencies must be made available on the PNDA Mirror in the usual way. For more details regarding working with the PNDA Mirror, see the PNDA Guide.

Put dependencies into the ```/mirror_apps``` directory on the PNDA Mirror.

