# Integrating VPP

Integrating VPP (fd.io Vector Packet processor) with PNDA is possible with the help of [vpp-monitoring-agent](https://github.com/pndaproject/magent), a VPP monitoring agent, which collects various stats from VPP and propagates them to Kafka.

Further information about the agent can be found on its [Github page](https://github.com/pndaproject/magent).

## End to end integration

This section contains an end to end example of integrating VPP with PNDA and performing some basic exploration of the data.

### Prerequisites

Following prerequisites are required to run this simple end-to-end setup.

#### Resources

Following systems are required to run this example:

1. PNDA
2. Linux VM running VPP and the monitoring agent

**Note: There are various options for PNDA deployment. The deployment itself is however out of scope of this section**

#### PNDA

Make sure you have one of the PNDA deployments up and running.

#### VPP VM

To setup a VPP VM:

1. Install a Ubuntu 64bit (desktop or server) VM
2. Install required VPP packages (more info on [VPP wiki](https://wiki.fd.io/view/VPP/Installing_VPP_binaries_from_packages)):

* To install VPP 1704, add following repository (/etc/apt/sources.list.d/99fd.io.list):

```
deb [trusted=yes] https://nexus.fd.io/content/repositories/fd.io.stable.1704.ubuntu.xenial.main/ ./
```

* To actually install the VPP packages:

```
sudo apt-get update && sudo apt-get install vpp vpp-api-python vpp-dev vpp-plugins
```

##### Build VPP monitoring agent from sources in VPP VM

Once VPP VM is ready, VPP monitoring agent can be deployed as well. To do this, follow the agent documentation on [Github page](https://github.com/pndaproject/magent).

**Note: Make sure to build the appropriate branch. When using VPP 1704 release, use 1704 branch**

**Note: Only build the agent, do not run it yet.**

##### Start VPP and agent

First step is to start the VPP itself:

```
sudo service vpp start
```

and check it's up and running by querying VPP interfaces:

```
sudo vppctl sh int
```

at least the *local0* interface should be shown.

Now that VPP is running, vpp monitoring agent needs to be pointed to the kafka broker. To do so, find the default agent configuration file (by default located at *GOPATH/src/pnda/vpp/monitoring/configuration.yaml*). There is a sample kafka producer configuration present. Make sure to uncomment it and set the right IP of Kafka. The result should look like:

```
    Type: producer.Kafka
    Configuration:
      Brokers:
        - 192.168.56.101:9092
      Format: json
      Topic: raw.log.localtest
    Aggregator: Global-aggregator
```

The last step is to start the monitoring agent. So, head back to the [Agent documentation](https://github.com/pndaproject/magent) and just start the agent according to the suggestions there.

**Note: To check what data is being pushed to Kafka, following file can be checked: */tmp/vpp-monitoring-log.json* which contains the same data written to disk in parallel.**

### Generating some data from VPP

The monitoring agent pulls some initial information from VPP such as: version, interface information etc. But to produce some additional data, create a loopback interface on VPP:

```
sudo vppctl create loopback interface
```

and then turn it on and off a couple times:

```
sudo vppctl set int state loop0 up && sudo vppctl set int state loop0 down
```

The agent will get the updates and push into KAFKA inside PNDA.

To verify, check the data stored in PNDA e.g. by checking following file:

```
cat /data/year\=2017/month\=7/day\=12/hour\=14/dump.json
```

### Exploring the data using Jupyter notebook

Open Jupyter notebook in your browser running in the PNDA e.g. http://192.168.56.101:9000. Select the tutorial notebook and open it. Now the data from VPP can be explored by:

1. Updating the path to data in the first cell.
2. Execute the first cell to load the data.
3. The second cell printing schema can be preserved and when executed, should display following schema:
```
root
 |-- stat: struct (nullable = true)
 |    |-- admin_state: boolean (nullable = true)
 |    |-- build_date: string (nullable = true)
 |    |-- build_directory: string (nullable = true)
 |    |-- interface_index: long (nullable = true)
 |    |-- interfaces: array (nullable = true)
 |    |    |-- element: struct (containsNull = true)
 |    |    |    |-- interface_index: long (nullable = true)
 |    |    |    |-- interface_name: string (nullable = true)
 |    |    |    |-- l2_address: string (nullable = true)
 |    |-- link_state: boolean (nullable = true)
 |    |-- program: string (nullable = true)
 |    |-- version: string (nullable = true)
 |-- stat_type: string (nullable = true)
 |-- timestamp: string (nullable = true)
 |-- vpp_uuid: string (nullable = true)
 |-- year: integer (nullable = true)
 |-- month: integer (nullable = true)
 |-- day: integer (nullable = true)
 |-- hour: integer (nullable = true)
```
4. The third cell can be used to do something more interesting like counting how many times we turned the *loop0* interface up. To do so, change the code in the cell to match:
```
# Select interface 1 (local0) "Going up" event
ifcUpEvents = testJsonData.filter("stat_type LIKE '%interfaceStateChange%' AND stat.interface_index LIKE '1' AND stat.admin_state LIKE 'true'").map(lambda x: x.stat)
ifcUpEvents.count()
```
5. After running the 3rd cell, you should see how many times the interface was enabled.

**Note: The data from VPP can be utilized in more interesting ways. Some additional examples are demonstrated in [VPP analytics with PNDA on Youtube](https://www.youtube.com/watch?v=6q06nFArAj8&t=3s)**

## Features

The agent currently produces following information about VPP:
* VPP version
* Interface info (ifc index, mac address etc.)
* Interface state updates
* Interface counters (RX, TX packets and bytes, drops)

The agent is designed in a way to be easily extensible. It is composed of 3 component types:

* Collectors - collect data from VPP using VPP's binary APIs
* Aggregators - aggragate, filter and batch stats from all the collectors
* Producers - push the aggregated stats into external system. Currently supported: Kafka, Filesystem

Developers can easily add new collectors and also producers if necessary.

## Future work

* The agent is written in Golang but relies on VPP's C API and calls them from Golang. Starting with VPP 1707, there will be Golang native API available for VPP, which could be utilized instead.
* The set of stats gathered by the agent can be expanded to cover more of VPP e.g. Bridge domain configuration, IPFIX etc.
