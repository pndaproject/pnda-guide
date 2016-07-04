# HDFS Cleaner 

This tool can be used to spawn multiple jobs to perform file clean up and data archival tasks when invoked by `cron` or any job scheduler.
 
## Directory cleanup

Typically of any system that runs multiple components or services, the log files may accumulate over time like running MapReduce jobs, spark applications, batch jobs etc.

HDFS cleaner can be configured to remove old files when either `age` or `size` threshold is reached by adding it as part of `properties.json`.

## Data Management

HDFS cleaner also interacts with Data service to perform data management. It either deletes old data when size or age threshold is reached or archive datasets on distributed storage like Swift or S3 containers.
It archives files under `archive` folder by tagging file with name of `datasource` and its `timestamp`

## How to restore archived files

Archived files can be copied back to the cluster using the `cp` or `distcp` commands. For example, to retrieve archived data under the `archive` folder in the `pnda` container, type the following command in an edge node terminal:

```
sudo -u hdfs hadoop distcp swift://archive.pnda/* hdfs://testcluster-cdh-mgr1:8020/user/pnda/
```