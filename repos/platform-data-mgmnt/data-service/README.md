# Data Service

This service helps operator to define data management policies for platform datasets with the PNDA console. It currently supports the following features:

* Age or Size based retention policy
* Data management mode to either archive or delete when condition is met
* Allow users to change policy from PNDA console


## Business logic

- Dataset exists on HDFS and is referenced in HBase table = dataset is listed with policy as per HBase table
- Dataset exists on HDFS and is not referenced in HBase table = dataset is listed with policy "no limit" 
- Dataset doesn't exist on HDFS but is referenced in HBase table = possible integrity problem, highlight this to operator

## Dataset location

The cluster location for datset are specified in config file and service reads this location looking for `source=` identifier. Each entry containing `source` is tagged as a dataset and its governed by policy set by the operator.

For example, in the case of PNDA, the default HDFS location for datasets is `/user/PNDA/datasets` and service will tag any subfolder on the basis of `source` tag as data entity

> Bulk datasets are not yet integrated with dataservice.

## Coming soon

 - Policy update in batches
 - Additional fields to determine incoming data arrival rate
 - Notifications when threshold is reached.

# Data Service 

The Data Service implements the following REST APIs:

## Dataset APIs 

### List datasets

This API will list all the datasets on the platform.

GET `http://192.168.100.74:7000/api/v1/datasets`

Response:

   {
    "status": "success",
    "data": [
      {
        "policy": "size",
        "path": "/user/pnda/PNDA_datasets/datasets/source=netflow",
        "max_size_gigabytes": 10,
        "id": "netflow",
        "mode": "archive"
      },
      {
        "policy": "keep",
        "path": "/user/pnda/PNDA_datasets/datasets/source=telemetry",
        "id": "telemetry",
        "mode": "delete"
      }
    ]
  }

### Dataset details

This API will return the details for a particular dataset.

GET `http://192.168.100.74:7000/api/v1/datasets/{netflow}`

Response:
  
    {
    "status": "success",
    "data": {
        "policy": "size",
        "path": "/user/pnda/PNDA_datasets/datasets/source=netflow",
        "max_size_gigabytes": 10,
        "id": "netflow",
        "mode": "archive"
    }
	}

### Update policy

This API can be used to update the policy of a dataset.

PUT `http://192.168.1.190:7000/api/v1/datasets/{netflow}`

BODY  

on basis of size:
	    
	    {
  			"policy":"size", 
  			"max_size_gigabytes":10
		}
		
on basis of age:

		{ 
          "policy":"age", 
		  "max_age_days":30 
        }

Response:
	 {
    	"status": "success",
    	"data": {
        "mode": "archive",
        "policy": "size",
        "path": "/user/pnda/PNDA_datasets/datasets/source=netflow",
        "id": "netflow",
        "max_size_gigabytes": 10
    	}
	}

### Update mode

This API can be used to change the mode of a dataset.

PUT `http://192.168.1.190:7000/api/v1/id/{netflow}`

BODY  
    
		{
  			"mode":"archive"
		}
		or
		{
			"mode":"delete"
		}

Response:

	     {
             "status": "success",
             "data": {
                "mode": "archive",
                "policy": "size",
                "path": "/user/pnda/PNDA_datasets/datasets/source=netflow",
                "id": "netflow",
                "max_size_gigabytes": 10
             }
          }

## Dataset partitions

This API will return all the partitions of a dataset.

GET `http://192.168.100.74:7000/api/v1/datasets/{netflow}/partitions`


Response:

    {
      "status": "success",
      "data": [
        "/user/pnda/PNDA_datasets/datasets/source=netflow/year=2015/month=11/day=01/hour=15",
        "/user/pnda/PNDA_datasets/datasets/source=netflow/year=2015/month=11/day=01/hour=16",
        "/user/pnda/PNDA_datasets/datasets/source=netflow/year=2015/month=11/day=01/hour=17",
        "/user/pnda/PNDA_datasets/datasets/source=netflow/year=2015/month=11/day=01/hour=18",
        "/user/pnda/PNDA_datasets/datasets/source=netflow/year=2015/month=11/day=01/hour=19",
        "/user/pnda/PNDA_datasets/datasets/source=netflow/year=2015/month=11/day=01/hour=20",
        "/user/pnda/PNDA_datasets/datasets/source=netflow/year=2015/month=11/day=01/hour=21",
        "/user/pnda/PNDA_datasets/datasets/source=netflow/year=2015/month=11/day=01/hour=22",
        "/user/pnda/PNDA_datasets/datasets/source=netflow/year=2015/month=11/day=01/hour=23",
        "/user/pnda/PNDA_datasets/datasets/source=netflow/year=2015/month=11/day=02/hour=00",
        "/user/pnda/PNDA_datasets/datasets/source=netflow/year=2015/month=11/day=02/hour=01",
        "/user/pnda/PNDA_datasets/datasets/source=netflow/year=2015/month=11/day=02/hour=02",
        "/user/pnda/PNDA_datasets/datasets/source=netflow/year=2015/month=11/day=02/hour=03",
        "/user/pnda/PNDA_datasets/datasets/source=netflow/year=2015/month=11/day=02/hour=04",
        "/user/pnda/PNDA_datasets/datasets/source=netflow/year=2015/month=11/day=02/hour=05",
        "/user/pnda/PNDA_datasets/datasets/source=netflow/year=2015/month=11/day=06/hour=09",
        "/user/pnda/PNDA_datasets/datasets/source=netflow/year=2015/month=11/day=06/hour=10",
        "/user/pnda/PNDA_datasets/datasets/source=netflow/year=2015/month=11/day=06/hour=11",
        "/user/pnda/PNDA_datasets/datasets/source=netflow/year=2015/month=11/day=06/hour=12",
        "/user/pnda/PNDA_datasets/datasets/source=netflow/year=2015/month=11/day=06/hour=13",
        "/user/pnda/PNDA_datasets/datasets/source=netflow/year=2015/month=11/day=06/hour=14",
        "/user/pnda/PNDA_datasets/datasets/source=netflow/year=2015/month=11/day=06/hour=15",
        "/user/pnda/PNDA_datasets/datasets/source=netflow/year=2015/month=11/day=06/hour=16",
        "/user/pnda/PNDA_datasets/datasets/source=netflow/year=2015/month=11/day=06/hour=17",
        "/user/pnda/PNDA_datasets/datasets/source=netflow/year=2015/month=11/day=06/hour=18"
      ]
    }
