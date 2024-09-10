# Developing Pipelines with Snowflake

You can create required database, schema and virtual warehouse in Snowflake UI known as Snowsight.

```sql
USE ROLE SYSADMIN;
CREATE DATABASE IF NOT EXISTS PIPELINE_DB;
CREATE SCHEMA IF NOT EXISTS PIPELINE_SCHEMA;
CREATE WAREHOUSE IF NOT EXISTS PIPELINE_WH WITH WAREHOUSE_SIZE = 'XSMALL';
```

In order to build a pipeline, you first have to have CSV file to ingest. In this case, we will use a sample CSV file 
from Snowflake sample data. To ingest data from local filesystem into Snowflake, you will have to upload the file to 
a Snowflake stage. A stage is Snowflake object that points to the location of data files in Cloud storage. When it 
comes to types of stages supported in Snowflake. There are two types.

1. External Stage: This is where data is stored in one of the supported public cloud providers' object storage.
2. Internal Stage: This is where data is stored in Cloud provider where the Snowflake account is hosted. There are 
   also different types of internal stages such as `User Stage`, `Table Stage`, `Named Stage` and `Temporary Stage`. 
   In brief, `User Stage` is a stage that is created by a user and is available only to that user. `Table Stage` is 
   a stage that is created by a user and is associated with a table. `Named Stage` is a stage that is more flexible 
   than `User Stage` and `Table Stage` and can be shared among multiple users. `Temporary Stage` is a stage that is created
    by a user and is available only for the duration of the session. In this case, we will use `Named Stage`.

You can create a named stage using below command.

```sql
USE DATABASE PIPELINE_DB;
USE SCHEMA PIPELINE_SCHEMA;
CREATE OR REPLACE STAGE CUSTOMER_STAGE;
```

You can list the contents of the stage by using `LIST` command.

```sql
LIST @CUSTOMER_STAGE;
```

From Snowsight, you can expand specific schema where you created the stage and you will the stages listed there. 
Click on the stage name and you will see the contents of the stage. You can upload the file by clicking on the Stage 
Details ta and then click on the `+Files` button at the top right corner. You can select the file from your local 
filesystem and upload it. At this point, the file in 
stage location doesn't have any schema associated with it. You can use below command to see the data in the file.

```sql
SELECT $1, $2, $3, $4, $5, $6 FROM @CUSTOMER_STAGE;
```

To load data from the file in the stage into a table, you can use below command.

```sql
CREATE 

Next, you can create a table in Snowflake to store the data from the CSV file. You can create a table using below

```sql
CREATE TABLE PIPELINE_DB.PIPELINE_SCHEMA.CUSTOMER
(
    CUSTOMER_ID NUMBER,
    CUSTOMER_NAME STRING,
    CUSTOMER_EMAIL STRING,
    CUSTOMER_PHONE STRING
);
```

## Snowflake Tasks

A Snowflake task is a scheduled job that runs a sequence of SQL statements. You can create a task using below 
command. Tasks are usually used with streams which detect changes in the table and then the task is triggered to 
ingest the data into another table.

```sql