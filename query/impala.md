# Impala 

[Apache Impala](http://impala.io) is the general purpose interface that clients use to retrieve the results of data processing carried out on the PNDA platform.

Impala is a distributed, massively parallel processing (MPP) database engine providing high-performance, low-latency SQL queries on data stored in Hadoop. It's designed for interactive use and typically returns results within seconds or a few minutes, rather than the many minutes or hours that are often required for standard Hadoop batch operations. It can access data directly from the HDFS file system or from HBase and also perform joins between these heterogeneous data stores.

In addition to straightforward data retrieval, Impala supports an extended subset of ANSI-92 SQL and so is powerful enough to provide analytical capabilities to clients over large datasets in Hadoop without requiring the coding required for Spark jobs.

## Metadata

In order to make use of Impala over data in Hadoop it is necessary to create metadata describing the desired mapping in the Hive Metastore.

For example, to make the data in `/user/pnda/important_data/set` available through Impala as a table `pndaA`:

	CREATE EXTERNAL TABLE pndaA
	(
	   id INT,
	   col_1 BOOLEAN,
	   col_2 DOUBLE,
	   col_3 TIMESTAMP
	)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
	LOCATION '/user/pnda/important_data/set';

If the data is structured, JSON for example, it could make sense to store it as parquet which allows for schema inference and efficient columnar aggregations:

	CREATE EXTERNAL TABLE pndaB
	LIKE PARQUET '/user/pnda/parquet/set/part-r-00001.parquet'
  	STORED AS PARQUET LOCATION '/user/pnda/parquet/set/';

As another example, to make data in a HBase table `dataset` available through Impala as a table `pndaC`:

	CREATE EXTERNAL TABLE pndaC (
	  id string,
	  bool_col boolean,
	  tinyint_col tinyint,
	  timestamp_col timestamp)
	STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
	WITH SERDEPROPERTIES (
	  "hbase.columns.mapping" =
	  ":key,bools:bool_col,ints:tinyint_col,strings:timestamp_col"
	)
	TBLPROPERTIES("hbase.table.name" = "dataset");

## Query

Impala supports a variety of mechanisms for executing queries following SQL-92 syntax.

Taking the above tables as an example:

	SELECT pndaA.col_1, pndaC.tinyint_col FROM pndaA, pndaC WHERE pndaA.id = pndaC.id

## Impala shell

The user can access the PNDA edge node and open the [impala shell](http://www.cloudera.com/documentation/cdh/5-1-x/Impala/Installing-and-Using-Impala/ciiu_impala_shell.html#impala_shell) which supports a variety of DML and DDL operations and allows ad hoc queries and data exploration.

## JDBC Connector

For Java developers, Cloudera have made available a [Impala JDBC Connector](http://www.cloudera.com/documentation/enterprise/latest/topics/impala_jdbc.html) which can be integrated into client applications. See this [example](https://github.com/onefoursix/Cloudera-Impala-JDBC-Example).

## ODBC Connector

Many client applications are able to make use of ODBC, for example the popular BI tool Tableau. Refer to the Cloudera documentation on the [Impala ODBC Connector](http://www.cloudera.com/documentation/cdh/5-1-x/Impala/Installing-and-Using-Impala/ciiu_impala_odbc.html?scroll=impala_odbc) for more details.

## Learn More about Impala

For a worked example of integrating a data processing application with HBase on PNDA, using Impala to access the result data refer to the DevNet page in the Developer menu.

Further reference material is available at the [Impala documentation](http://www.cloudera.com/documentation/archive/impala/2-x/2-1-x/topics/impala_tutorial.html) site.
