# Queue Placement.

## Introduction.
Hadoop distributions come with a resource management system. The ResourceManager has two main components: Scheduler and ApplicationsManager. Traditionally organization have a separate set of compute resources for development workloads and for productions workloads. This not only leads to poor average resource utilization and overhead of managing multiple independent clusters but more importantly to duplication of the data, which represent a considerable cost in a big data platform. Consequently, sharing data lakes between these two activities represent considerable cost-savings in infrastructure resources. However, sharing computes resources for production activities with development activities should be done in all respect of the critical SLA's production workloads have the abide by. In a default PNDA deployment, the yarn schedulers have been configured to prioritize the system functionality first (in order not to lose any data), then the production workload and finally as last priority the development applications if any resources are still available. Unfortunately, the Yarn schedulers and especially their queue placement tools are more designed around sharing resources across organizations rather than for a priority based queueing system. For this reason, PNDA has chosen to complement the queue placement policies with its own tool and configuration options.

This document describes the queue placement architecture provided by PNDA. PNDA provides a pluggable policy that applies to both Hadoop schedulers and allows YARN applications to be placed in queue in a consistent way.

## Pluggable.
The policy definitions in PNDA are placed on every hadoop node and the Jupyter node in the policies directory under the [resource-manager path](https://github.com/pndaproject/platform-salt/blob/a2d961b212e0745635422e791cf311242dbf339c/pillar/services.sls#L96). The applicable policy is linked to by the file [policy_file](https://github.com/pndaproject/platform-salt/blob/a2d961b212e0745635422e791cf311242dbf339c/pillar/services.sls#L97).

## Default policy and configuration.

The default policy is the 'yarn-user-group-policy.sh', which is configured through a map.txt file located in the same 'policies' subdirectory. The mapping file defers slightly between the fair scheduler (used in CDH) and the capacity scheduler (used in HDP) in the queue definition. The fair scheduler requires the queue hierarchy to be encoded into queue target (example: root.applications.prod) whereas for the capacity scheduler, just the leaf name needs to be specified as leaf name uniqueness is guaranteed by the framework.

The syntax supported by the mapping file consist of a target followed by a space followed by a queue name (<target> <queue>). All rules are scanned from top to bottom.
<br>If no specific queue has been requested, then the first matching rule will return the associated queue.
<br>If a specific queue has been requested, then all the rules will be scanned until a rule validates the requested queue.
The target can be defined with one of the following syntax.
1) A user based rule (user name followed by a semi colon):
<br>```username:```
<br>This rule applies to a single used defined by the `username` (irrespective of his group membership).
<br>Example: ```pnda: root.default```
2) A group based rule (semi colon followed by a group name):
<br>```:groupname```
<br>This rule applies to all members of the group defined by the `groupname` (irrespective of the username).
<br>Example: ```dev root.applications.dev```
3) A single wildcard based rule (`asterisk`):
<br>```*```
<br>This rule applies to all users irrespective of their name/group. Such a rule can typically be used as:
<br>1) last rule to place all remaining users that didn't match any of the previous rules in a specific queue.
<br>2) only rule to place all applications in the same queue.
<br>Example: ```* root.default```

Finally, here is an example where multiple rules for a same target can apply:
```
:prod root.applications.prod
:prod root.applications.dev
```
In this case, an application submitted by a user which is a member of group prod would be placed in the `root.applications.prod` queue. But if the user would explicitly reques the `root.applications.dev` through a cli or UI then the application woul dbe placed in the requested queue provided a rule validates the request.
Example: `sudo -u prod1 spark-shell --queue root.applications.dev` would cause the first rule above to be skipped but would get validated by the second rule.  

Default mapping configuration for the fair scheduler: [fair_scheduler_map.txt](https://github.com/pndaproject/platform-salt/blob/a2d961b212e0745635422e791cf311242dbf339c/salt/resource-manager/templates/fair_scheduler_map.txt)

## Applicability.
Currently, the following cli's and frameworks have been configured to use the PNDA queue placement policy:
1) CLI's: `spark-submit`, `spark-shell`, `pyspark`, `mapred`
2) Jupyter notebooks using the pyspark kernel.
3) Applications deployed through the deployment manager.
