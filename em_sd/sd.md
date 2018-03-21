# Service discovery

Now that there is the consul infrastructure, we can rely on it to register/deregister services with checks. This could done by settings up some JSON file in Consul, as for exemple, for the Zookeeper service, you define a service and health check per Zookeeper instance as follow:

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

As you can see, you first declare a service instance linked to a check to ensure that the service is healthy. Check out the Consul documentation for more details [Consul Catalog HTTP API](https://www.consul.io/api/catalog.html)

But we should also use Consul anouncer which is part of the Consul Community tools and provide an easy way to register/deregister consul services. Using Consul anouncer is easy, you still need the JSON description file as above, but then, instead of adding it in the Consul configuration files folder, you need to specify it once running the service. As for example, for kafka manager, we will have this JSON configuration file:

```
{
    "service": {
        "name": "kafka-manager",
        "ip": "10.0.1.228",
        "port": 10900
    }
}
```

and on the service file /etc/init/kafka-manager.conf (on Ubuntu):
```
exec consul-announcer --config=@/opt/pnda/consul/km.json --agent 10.0.1.36:8500 --/opt/pnda/kafka-manager/bin/kafka-manager -Dconfig.file=/opt/pnda/kafka-manager/conf/application.conf -Dapplication.home=/opt/pnda/kafka-manager -Dhttp.port=10900
```

Also, you can see the service topology through the Consul UI on the service part:
![Consul Service](consul_services.jpeg)
