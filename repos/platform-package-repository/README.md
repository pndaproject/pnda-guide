# Platform Package Repository #

## Overview

Packages are independently deployable units of application layer functionality. Each package consists of one or more components, each of which has a defined type. The `platform-deployment-manager` documentation details the format and usage of packages.

The platform-package-repository component provides a REST API that allows:

 - a package to be uploaded
 - a package to be downloaded
 - the contents of repository to be listed

## Repository API

By default, the packages API is available at port `8888` of the `edge` node.

### List packages from the repository

?recency=n may be used to control how many versions of each package are listed, by default recency=1
````
GET /packages

Response Codes:
200 - OK
500 - Server Error

Example response:
[
  {
    "latest_versions": [
      {
        "version": "1.0.23",
        "file": "spark-batch-example-app-1.0.23.tar.gz"
      }
    ],
    "name": "spark-batch-example-app"
  }
]
````

### Get package contents
````
Downloads a package from the repository
GET /packages/<package>

Response Codes:
200 - OK
404 - Not Found
500 - Server Error

Response body:
The binary contents of the package that was uploaded
````

### Upload a package to the repository
````
PUT /packages/<package>

Response Codes:
200 - Accepted
500 - Server Error

e.g.
curl http://host:8888/packages/my-package-1.0.0.tar.gz --upload-file my-package-1.0.0.tar.gz

````
### Delete a package from the repository
````
DELETE /packages/<package>

Response Codes:
200 - Accepted
404 - Not Found
500 - Server Error

e.g.
curl -X DELETE http://host:8888/packages/my-package-1.0.0.tar.gz
````
