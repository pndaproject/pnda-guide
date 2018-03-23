# Service discovery

The Consul infrastructure can be used to register/deregister services automatically based on host health. More services will be defined in due course and it is also possible to configure user defined services by editing the Consul configuration files. Here is an example of the configuration needed to configure a Zookeeper service:

```
{
    "service": {
        "id": "zookeeper1",
        "name": "zookeeper",
        "tags": ["1"],
        "address": "10.0.1.123",
        "port": 2181
    },
    "check": {
        "id": "service:zookeeper1",
        "name": "Zookeeper health check",
        "ServiceID": "zookeeper1",
        "args": ["/opt/pnda/zookeeper-3.4.11/consul_check.sh"],
        "interval": "60s",
        "timeout": "3s"
    }
}
```

A service instance is declared and then linked to a check to ensure that the service is healthy. Review the Consul documentation for more details [Consul Catalog HTTP API](https://www.consul.io/api/catalog.html)

Also, you can see the service topology through the Consul UI on the service part:
![Consul Service](consul_services.jpeg)
