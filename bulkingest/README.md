# Bulk Ingest

In addition to streaming ingest via Kafka producers, PNDA also provide an offline bulk ingest tool for those who would like to migrate pre-collected to PNDA. 

Unlike streaming ingest, bulk ingest does not require data to be formatted using the PNDA schema, and places no restrictions on the destination folder (as long as the user has write permission). 

## [Bulk Ingest Tool](https://github.com/pndaproject/platform-tools/bulkingest/blob/master/README.md)

The bulk-ingest tool can be used up loading a file or folder to HDFS. Once the dataset has been uploaded, it can be found in `/user/pnda/PNDA_datasets/bulk/`.
