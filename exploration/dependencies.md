# Adding application dependencies for PySpark applications

## PySpark client driver ##

These packages satisfy dependencies for code running client side in the driver process. For example, library functions used to process result data from distributed jobs running on the platform.

These are [python pip dependencies](https://pypi.python.org/pypi) and are specified in the format required by [pip](https://pypi.python.org/pypi/pip). Refer to https://packaging.python.org/guides/tool-recommendations/ for more information.

Maintain this file:

```platform-salt/salt/app-packages/files/app-packages-requirements.txt```

Also ensure the PNDA mirror contains the corresponding packages.

### Updates ###

If it becomes necessary to update the installed dependencies on PNDA, run this Salt State:

```sudo salt -C 'G@hadoop:role:EDGE or G@roles:jupyter' state.sls app-packages```

## Usage ##

Use dependencies in the normal fashion, for example:

```import sharedroutines```

## PySpark executor or cluster driver ##

These packages satisfy dependencies for code running in executors on the cluster. For example, algorithms used to compute result data in distributed jobs running on the platform.

These are typically Python [eggs](https://setuptools.readthedocs.io/en/latest/formats.html).

Maintain this file:

```platform-salt/salt/app-packages/files/app-packages-hdfs.txt```

## Updates ##

Run this Salt State to stage the listed egg files at the HDFS path configured in the Pillar (see platform-salt/pillar/pnda.sls):

```sudo salt-call state.sls hdfs-sync```

Also ensure the PNDA mirror contains the corresponding packages.

### Usage ###

In your PySpark environment, use the SparkContext.addPyFile method to add any required dependencies, referencing the full HDFS path configured as mentioned above. For example:

```sc.addPyFile('hdfs:///user/deployment/app_packages/sharedroutines-0.1.egg')```

## Mirrors ##

Python pip dependencies must be deployed on the PNDA Mirror in the usual way. For more details regarding working with the PNDA Mirror, see the PNDA Guide.

Python egg files must be deployed in a ```/mirror_apps``` directory on the PNDA Mirror.

