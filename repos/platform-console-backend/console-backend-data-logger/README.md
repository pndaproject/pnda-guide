# Data Logger API

The console-backend-data-logger proejct contains a Node.js app that can be called by other services to contribute data to be displayed in the console.

By default, the data manager runs on port 3001. 

For an interactive Swagger API console, see __/docs__.

For some Frisby unit tests, see __/tests__.

## POST Metrics API

This API can be used to add a metric value. Note that the value will overwrite the previous value for the same metric, if any.

````    
POST /metrics

{
	"data": [
  	{
  	  "source": "zookeeper",
  		"metric": "zookeeper.health",
  		"value": "OK",
  		"causes": "Everything is fine!",
  		"timestamp": 1234567890
  	}
	],
	"timestamp" : 123456789
}
````

## POST Packages API

This API can be used to create a package.

````    
POST /packages

{
	"data": [
	  {
		  "id": "package1",
		  "state": "running",
		  "timestamp": 1234567890
	  }
	],
	"timestamp" : 123456789
}
````

## POST Applications API

This API can be used to create an application.

````    
POST /applications

{
	"data": [
	  {
		  "id": "application2",
		  "state": "stopped",
		  "timestamp": 1234567890
 	  }
	],
	"timestamp" : 123456789
}
````

