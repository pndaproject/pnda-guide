# Queue Placement

## Introduction
Hadoop distributions come with a resource management system. The ResourceManager has two main components:
 - Scheduler
 - ApplicationsManager

Traditionally organizations have a separate set of compute resources for development workloads and for productions workloads. This not only leads to poor average resource utilization and overhead of managing multiple independent clusters but more importantly to duplication of the data, which represent a considerable cost in a big data platform. Consequently, sharing data lakes between these two activities represent considerable cost-savings in infrastructure resources. However, sharing compute resources for production activities with development activities should be done in all respect of the critical SLA's production workloads have the abide by.

In a default PNDA deployment, the YARN schedulers have been configured to prioritize the system functionality first (in order not to lose any data), then the production workload and finally as last priority the development applications if any resources are still available. Unfortunately, the Yarn schedulers and especially their queue placement tools are more designed around sharing resources across organizations rather than for a priority based queueing system. For this reason, PNDA has chosen to complement the queue placement policies with its own tool and configuration options.

This document describes the queue placement architecture provided by PNDA. PNDA provides a pluggable policy that applies to both Hadoop schedulers and allows YARN applications to be placed in queue in a consistent way.

## Pluggable
The policy definitions in PNDA are placed on every hadoop node and the Jupyter node in the policies directory under the [resource-manager path](https://github.com/pndaproject/platform-salt/blob/a2d961b212e0745635422e791cf311242dbf339c/pillar/services.sls#L96). The applicable policy is linked to by the file [policy_file](https://github.com/pndaproject/platform-salt/blob/a2d961b212e0745635422e791cf311242dbf339c/pillar/services.sls#L97).

## Default policy and configuration

The default policy is the 'yarn-user-group-policy.sh', which is configured through a map.txt file located in the same 'policies' subdirectory. The mapping file defers slightly between the fair scheduler (used in CDH) and the capacity scheduler (used in HDP) in the queue definition. The fair scheduler requires the queue hierarchy to be encoded into queue target (example: root.applications.prod) whereas for the capacity scheduler, just the leaf name needs to be specified as leaf name uniqueness is guaranteed by the framework.

The syntax supported by the mapping file consist of a target followed by a space followed by a queue name (\<target\> \<queue\>). All rules are scanned from top to bottom as follow:
1) If no specific queue has been requested, then the first matching rule will return the associated queue.
2) If a specific queue has been requested, then all the rules will be scanned until a rule validates the requested queue.

The \<target\> can be defined with one of the following syntaxes:
* A user based rule (user name followed by a semi colon):
<br>```username:```
<br>This rule applies to a single used defined by the `username` (irrespective of his group membership).
<br>**Example:** ```pnda: root.default```

* A group based rule (semi colon followed by a group name):
<br>```:groupname```
<br>This rule applies to all members of the group defined by the `groupname` (irrespective of the username).
<br>**Example:** ```dev root.applications.dev```

* A single wildcard based rule (`asterisk`):
<br>```*```
<br>This rule applies to all users irrespective of their name/group. 
<br>**Example:** ```* root.default```
<br>Such a rule can typically be used as:
   * last rule to place all remaining users that didn't match any of the previous rules in a specific queue.
   * only rule to place all applications in the same queue. 

Finally, here is an **example** when multiple rules for a same target (see bullet 2 above) can apply:
```
:prod root.applications.prod
:prod root.applications.dev
```
In this example, an application submitted by a user, who is a member of group prod, would by default be placed in the `root.applications.prod` queue. But if the user explicitly requests the `root.applications.dev` through a cli parameter or UI property then the application would be placed in the requested queue provided a rule validates the requested queue.
<br>**Example**: `sudo -u prod1 spark-shell --queue root.applications.dev` would cause the first rule above to be skipped but would get validated by the second rule.  

Default mapping configuration for the fair scheduler: [fair_scheduler_map.txt](https://github.com/pndaproject/platform-salt/blob/a2d961b212e0745635422e791cf311242dbf339c/salt/resource-manager/templates/fair_scheduler_map.txt)
Default mapping configuration for the capacity scheduler: [capacity_scheduler_map.txt](https://github.com/pndaproject/platform-salt/blob/5a3e41549afa3ad3d263826024d73c5fd91da703/salt/resource-manager/templates/capacity_scheduler_map.txt)

## Applicability
Currently, the following cli's and frameworks have been configured to use the PNDA queue placement policy:
1) CLI's: `spark-submit`, `spark-shell`, `pyspark`, `mapred`, `hive`, `beeline`, `flink`, `pyflink`, `start-scala-shell.sh`
2) Jupyter notebooks using the pyspark kernel.
3) Applications deployed through the deployment manager.

## Default overwrite
As explained above, the first applicable rule will be used to define the queue. But when multiple queues are applicable, for some application, a preference for a particular queue can still be expresse. For example:
1) For the `spark-submit`, `spark-shell`, `pyspark` and `mapred`CLI's this can be expressed using the `--queue <queuename>` option as explained in their respective documentation.
2) For the `hive` and `beeline` CLI's this can be expressed through the `--hiveconf tez.queue.name=<queuename>` option as as explained in their respective documentation.
3) For Oozie applications, this can be expressed by defining an the action specific configurations. For example, with a mapreduce action, the [mapreduce.job.queuename](https://hadoop.apache.org/docs/r2.6.3/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml) Hadoop JobConf property can be specified as explained in [Map-Reduce Action](https://oozie.apache.org/docs/4.1.0/WorkflowFunctionalSpec.html#a3.2.2_Map-Reduce_Action) to express a queue preference. If the user defined a `mapreduce.job.queuename` property in the config-default.xml, then that queue will be prefered and otherwise the deployment manager will inject a matching queue itself before submission to Oozie.
