# Create Data Warehouse in Snowflake

There are multiple ways to create data warehouse in Snowflake. You can use the Snowflake UI, SnowSQL, or Snowflake API to create a data warehouse. In this lesson, we will see how to create a data warehouse using Snowflake UI.

In order to create a data warehouse, you need one of the following roles:
- ACCOUNTADMIN
- SYSADMIN

## Create Warehouse using Snowflake UI

Once you have switched to one of these roles from the Snowflake UI at the bottom left corner, you can now see the 
`WAREHOUSES` button on the top right hand side corner.

![Snowflake Warehouse](./create-warehouse.png "Snowflake Create Warehouse")


You can simply click this button. Provide a name for the warehouse, select the size of the warehouse. You can pick 
X-Small for learning purposes and it will be sufficient for most learning cases. Notice that the size of the 
warehouse changes by the number of credits consumed per hour. 

Under the Advanced Options, you can set the Auto 
resume and Auto suspend options. Auto resume will automatically resume the warehouse when any query requires a 
warehouse and Auto suspend will automatically suspend the warehouse after a period of inactivity. This can be useful 
for saving your credits. There is also option for Multi-Cluster warehouse which can be used for scaling the 
warehouse across multiple clusters. You can also set the minimum and maximum number of clusters for the warehouse. 
This can be useful for large scale organizations where you need to scale the warehouse based on the load. Scaling 
policy can be set to `Standard` or `Elastic`. Standard scaling policy will be quicker to scale up. The first cluster 
actually starts up immediately. Economy scaling policy will be more focused on cost effectiveness. The warehouse 
starts up if the system estimates enough load to keep cluster busy for at least 6 minutes.

Query Acceleration option can be used to enable the warehouse for query acceleration. This can be useful for 
flexible workloads to finish quickly.

Once you have created the cluster, you will be able to select the warehouse form the list of warehouses for a 
specific SQL worksheet.

## Create Warehouse using SnowSQL

You can also create a warehouse using SQL command which can be useful for code documentation and reuse. You can use the 
below command to create a warehouse.

```sql
CREATE OR REPLACE WAREHOUSE DEMO_WH
    WITH
    WAREHOUSE_SIZE = 'X-SMALL'
    AUTO_SUSPEND = 60
    AUTO_RESUME = TRUE
    SCALING_POLICY = 'STANDARD'
    MIN_CLUSTER_COUNT = 1
    MAX_CLUSTER_COUNT = 1
    INITIALLY_SUSPENDED = TRUE;
```

The multi cluster warehouse is only available in Enterprise edition of Snowflake. So, above command may not work if 
you don't have Enterprise Edition of Snowflake. For Standard Edition, you can use below command.

```sql
CREATE OR REPLACE WAREHOUSE FIRST_WH
    WITH
    WAREHOUSE_SIZE = 'X-SMALL'
    AUTO_SUSPEND = 60
    AUTO_RESUME = TRUE
    INITIALLY_SUSPENDED = TRUE;
```

## Modify Warehouse

You can also modify existing warehouse using the `ALTER WAREHOUSE` command. You can use the below command to modify 
the AUTO_SUSPEND and AUTO_RESUME options for the warehouse.

```sql
ALTER WAREHOUSE DEMO_WH
    SET AUTO_SUSPEND = 120
    AUTO_RESUME = TRUE;
```

Above command modifies the `DEMO_WH` warehouse to suspend after 120 seconds of inactivity and resume automatically 
when a query is run.

## Drop Warehouse

You can also drop the warehouse using the `DROP WAREHOUSE` command. 

```sql
DROP WAREHOUSE DEMO_WH;
```