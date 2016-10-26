# Platform Package Repository #

## Overview

Packages are independently deployable units of application layer functionality. Each package consists of one or more components, each of which has a defined type. The `platform-deployment-manager` documentation details the format and usage of packages.

The platform-package-repository component provides a REST API that allows:

 - a package to be uploaded
 - a package to be downloaded
 - the contents of repository to be listed

## Repository configuration

All the configuration is defined in the file pr-config.json. Currently, the package repository supports Swift / AWS S3 or file system backend storage. 

Here are some example configurations for each supported option.

- Swift: use the settings from the openrc script available through the OpenStack Horizon console. This assumes that the apps container with the folder releases is already created and also that the user has access to Swift.

```json
    "SwiftRepository": {
        "access": {
            "account":"OS_PROJECT_NAME",
            "user": "OS_USERNAMAE",
            "key": "OS_PASSWORD",
            "auth_url": "OS_AUTH_URL"
        },
        "container": {
            "container": "apps",
            "path": "releases"
        }
    },
```

- AWS S3:
```json
    "S3Repository": {
        "access": {
            "region": "AWS_REGION",
            "access_key": "AWS_KEY",
            "secret_access_key": "AWS_SECRET_KEY"
        },
        "container": {
            "bucket": "apps",
            "path": "releases"
        }
    },
```
- File system: this could be local filesystem, sshfs or mounted volume but is transparent from a package repository point of view.
```json
    "FsRepository": {
        "location": {
            "path": "/opt/packages"
        }
    }
```

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
