# Platform Data Management

This project contains PNDA data management modules.

## [Data Service](data-service/README.md)

This module has a REST API that lets operators browse available datasets, and modify data rentention policies. This API is called by the `platform-console-backend` service, which is in turn called by the `platform-console-frontend` website.

## [HDFS Cleaner](hdfs-cleaner/README.md)

The submodule is invoked as part of a cron scheduler to clean up or archive datasets according to the policies defined in the data-service.

## [Oozie Templates](oozie-templates/README.md)

This module contains sample templates that can be used to archive or delete incoming data as defined by the policies. 