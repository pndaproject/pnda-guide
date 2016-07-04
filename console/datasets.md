# Datasets

The datasets page lets you manage the data retention policy of each dataset in the cluster. As a big data system, PNDA is capable of storing a large amount of data. However, to manage your data storage costs you can choose to put an upper limit on the amount of data that is retained. 

All data stored in PNDA is divided into datasets. Each dataset has a unique identifier, such as `netflow`, `telemetry`, etc. You can customize the data retention policy for each dataset individually, as your needs may be different for each kind of data. 

If you need to retain data indefinitely, you can choose to keep it. Otherwise you can choose what happens to data when it has reached the limit. If you need to retain the data after it is removed from PNDA, you can choose to have it archived. If you no longer need the data, you can have it deleted. 

You can limit the amount of data by age or by size. If you choose to limit the data by age, you can specify the maximum age in days. Alternatively if you choose to limit the data by size, you can specify the maximum size in gigabytes. 

![Datasets](images/datasets_none.png)

## Setting policies

The datasets page contains a table that lists all datasets in the current cluster, showing the current data retention policy. You cannot add datasets on this page, only modify the policy for existing datasets.

- From the mode popup menu, choose whether you want to keep data indefinitely, or archive or delete data that has exceeded the limit. 
- From the policy popup menu, choose whether you want to limit data by age or by size. 
- In the limit column, you can enter the maximum age in days, or size in gigabytes, depending upon the policy.  

After you have made changes, click the Save button to review and confirm the changes that will be made. For example, if you are changing the limit from 30 to 20 gigabytes, the message will confirm that up to 10 gigabytes of data could be archived or deleted.

## Creating datasets

Datasets are automatically created based on the name of the `src` field in Kafka messages by [gobblin](../repos/gobblin/README.md). See the [getting started](../gettingstarted/README.md#producer-integration) for more information on how datasets are created.  

## Kafka

Kafka has its own data rention policy that affects how long data from producers stays in the queue for consumers to consume it. By default, Kafka keeps data for 24 hours for each topic. You can use the Kafka Manager to override the default data retention policy on a time or size basis. 