# Oozie Templates

Contains sample **Oozie Coordinator** templates to archive content to **OpenStack** containers

## Enable Swift support for Hadoop

Enable swift support by adding following properties as part of `core-site.xml`. The `core site.xml` needs to updated on all nodes of Cluster.
```
<property>
      <name>fs.swift.impl</name>
      <value>org.apache.hadoop.fs.swift.snative.SwiftNativeFileSystem</value>
    </property>
   <property>
        <name>fs.swift.service.team.auth.url</name>
        <value>https://eu-london-1.cloud.cisco.com:5000/v2.0/tokens</value>
   </property>
   <property>
        <name>fs.swift.service.team.username</name>
        <value>*****</value>
    </property>
    <property>
        <name>fs.swift.service.team.tenant</name>
        <value>teamanalytics</value>
    </property>
    <property>
        <name>fs.swift.service.team.region</name>
        <value>eu-london-1</value>
    </property>
    <property>
        <name>fs.swift.service.team.password</name>
        <value>*****^</value>
    </property>
    <property>
        <name>fs.swift.service.team.public</name>
        <value>true</value>
    </property>
```

> The sample was tried when Cisco Cloud services was not supporting 'keyfile' based authentication. If it's supported, then it is safe to use 'keyfile' file than passwords.

Copy the [Hadoop-OpenStack](http://mvnrepository.com/artifact/org.apache.hadoop/hadoop-openstack/2.7.1) jar to either /usr/lib/hadoop/lib or to Cloudera parcel folders

## Verfiy SWIFT container is accessible within HDFS

File system URLs are of the form `swift://acontainer.aservice/path/to/files` in this specific case based on above parameters the URL will translate to `swift://archive.team/path/to/files`.

Run sample commands like cp, mv, rm to verify whether service is working well HDFS fs -cp /user/xxx/xx  swift://archive/team/path_to_files 

## Filling in Coordinator and Workflow variables to suit archiving requirements

Change following variables in Job.properties to match data rate and incoming frequency

* `COORD_START_TIME= *Coordinator start time*`
* `COORD_END_TIME=  *Coordinator end time*`
* `COORD_TIMEOUT = *timeout for Coordinator action*`
* `COORD_FREQ = *Frequency at which the coordinator need to run*`


## Configuring datasets to archive

The datasets that needs to be archived to *swift* container is specified as part of `common_datasets.xml`. The data partition startegy defines the input events for coordinator run. 

    ${baseDataDir}/${YEAR}-${MONTH}-${DAY}-${HOUR}

## Running coordinator using Oozie CLI

    oozie job -oozie http://localhost:8080/oozie -config job.properties -run






