# Using Python application dependencies

## Jupyter, Deployment Manager and shells

### Anaconda and app-packages dependencies

For dependencies managed via Anaconda or the app-packages mechanism, simply import the dependencies required in the normal way. PNDA has already set up the necessary paths and depedendency caches so nothing further is required.

### Runtime dependencies

For dependencies to be delivered at runtime, use addPyFile() in your code. For example -

    addPyFile('hdfs:///pnda/deployment/app_packages/sharedroutines-0.1.egg')

## Batch

At present, PNDA does not set these up automatically in the same way as above.

For jobs to be scheduled via coordinators and workflows, do the following.
 
### Anaconda dependencies

To your <spark-opts> section in the workflow action, add

     --conf spark.executorEnv.PYSPARK_PYTHON=/opt/pnda/anaconda/bin/python
     --conf spark.yarn.appMasterEnv.PYSPARK_PYTHON=/opt/pnda/anaconda/bin/python

### Dependencies managed via the app-packages mechanism

Due to [this unresolved issue in Spark](https://issues.apache.org/jira/browse/SPARK-22151), spark.yarn.appMasterEnv.PYTHONPATH isn't handled properly. Until this is resolved:

Add this to your code, before importing dependencies -

    sys.path.insert(0, '/opt/pnda/app-packages/lib/python2.7/site-packages')
 
### Runtime dependencies

For dependencies to be delivered at runtime, use addPyFile() in the normal way as described above.
