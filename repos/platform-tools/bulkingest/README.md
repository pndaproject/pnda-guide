# Bulk Ingest

The `ingest.sh` shell script is used to ingest data onto a PNDA cluster.

## Install

To install dependencies and configure the tool for an HttpFS endpoint, run the `install` command with the IP address of a node running `HttpFS` role and port 14000. 

```
 ingest.sh install http://host:port
 e.g 
 ingest.sh install http://192.168.0.0:14000
```

Please check with your cluster administrator for the correct IP address and port, as it may change in production deployments. 

## Upload

To upload file or directory onto a cluster, run the `upload` command with the dir/file name. 
You can use the `-f` flag to overwrite existing files, and `-t number of threads` for parallelism.  
 
```
ingest.sh upload localfile or local_directory
e.g
ingest.sh upload Readme.txt
ingest.sh upload -f -t 10 /user/data
```

Once the upload completes, verify whether the transferred files are stored in the `/user/pnda/PNDA_datasets/bulk/` folder in HDFS.

## Dependencies

The tool depends on the `hdfs` python pip package. The `install` command when run also sets up the package, as well as populating the cli config.

# Known bugs

- Sometime the files or nested directories are not overwritten for large folders. In such case rerun the upload command with -f switch.
- Appending a '/' slash at the end seem to have weird effect, in some case even overwriting the directory. To upload a directory just use its name as the argument.
