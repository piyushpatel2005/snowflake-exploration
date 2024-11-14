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

Next, you can create a table in Snowflake to store the data from the CSV file. You can create a table using below

```sql
CREATE TABLE PIPELINE_DB.PIPELINE_SCHEMA.CUSTOMER_STG
(
    CUSTOMER_ID NUMBER,
    CUSTOMER_NAME STRING,
    CUSTOMER_EMAIL STRING,
    CUSTOMER_PHONE STRING,
    FILENAME STRING,
    CREATED_AT TIMESTAMP
);
```

Next, you can use `COPY INTO` command to load the data from the file in the stage into the table. Here 
`metadata$filename` is a special column that contains the name of the file from which the data is loaded.

```sql
COPY INTO CUSTOMER_STG
FROM (
    SELECT $1, $2, $3, $4, metadata$filename, CURRENT_TIMESTAMP() FROM @CUSTOMER_STAGE
)
FILE_FORMAT = (TYPE = CSV, skip_header = 1)
ON_ERROR = ABORT_STATEMENT
purge = true;
```

The `ON_ERROR` parameter specifies the action to take if an error occurs during the load operation. The 
`ABORT_STATEMENT` option specifies that the statement should be aborted if an error occurs. The `PURGE` option is 
used to delete the file from the stage after the data is loaded into the table. You can verify the `@CUSTOMER_STAGE`.

```sql
LIST @CUSTOMER_STAGE;
```

You can query the table to see the data loaded from the file.

```sql
SELECT * FROM CUSTOMER_STG;
```

Next, you want to keep a final table in Snowflake where you want to store the data from the staging table. This 
table will be in columnar data format and may also get few columns from other tables.  You can 
create a table using below command.

```sql
CREATE TABLE PIPELINE_DB.PIPELINE_SCHEMA.CUSTOMER
(
    CUSTOMER_ID NUMBER,
    CUSTOMER_NAME STRING,
    CUSTOMER_EMAIL STRING,
    CUSTOMER_PHONE STRING,
    FILENAME STRING,
    CREATED_AT TIMESTAMP
);
```

Here, you want to ensure that only the updated records are inserted into the final table. You can use `MERGE INTO` 
command to ensure this.

```sql
MERGE INTO CUSTOMER AS T
USING CUSTOMER_STG AS S
ON T.CUSTOMER_ID = S.CUSTOMER_ID
WHEN MATCHED THEN
    UPDATE SET
        T.CUSTOMER_NAME = S.CUSTOMER_NAME,
        T.CUSTOMER_EMAIL = S.CUSTOMER_EMAIL,
        T.CUSTOMER_PHONE = S.CUSTOMER_PHONE,
        T.FILENAME = S.FILENAME,
        T.CREATED_AT = S.CREATED_AT
WHEN NOT MATCHED THEN
    INSERT (CUSTOMER_ID, CUSTOMER_NAME, CUSTOMER_EMAIL, CUSTOMER_PHONE, FILENAME, CREATED_AT)
    VALUES (S.CUSTOMER_ID, S.CUSTOMER_NAME, S.CUSTOMER_EMAIL, S.CUSTOMER_PHONE, S.FILENAME, S.CREATED_AT);
```

This command will ensure that for `CUSTOMER_ID` that already exists in the `CUSTOMER` table, the record will be unique.

```sql
SELECT * FROM CUSTOMER;
```

## Tranformations

You can define your transformations in SQL. In this case, let's define a summary table that will store the count of 
customers every day. You can create a table using below command.

```sql
CREATE TABLE PIPELINE_DB.PIPELINE_SCHEMA.CUSTOMER_SUMMARY
(
    DATE DATE,
    CUSTOMER_COUNT NUMBER
);
```

You can load the data into this summary table using `INSERT INTO` command.

```sql
INSERT INTO CUSTOMER_SUMMARY (DATE, CUSTOMER_COUNT)
SELECT DATE_TRUNC('DAY', CREATED_AT), COUNT(*)
FROM CUSTOMER
GROUP BY DATE_TRUNC('DAY', CREATED_AT);
```

```sql
SELECT * FROM CUSTOMER_SUMMARY;
```


## Snowflake Tasks

A Snowflake task is a scheduled job that runs a sequence of SQL statements. Once you've tested your SQL statements, 
you can create a task using below command. This will only create the task and not execute it.

```sql
USE DATABASE PIPELINE_DB;
USE SCHEMA PIPELINE_SCHEMA;
CREATE TASK CUSTOMER_SUMMARY_TASK
WAREHOUSE = PIPELINE_WH
SCHEDULE = '5 minute'
AS
BEGIN
    TRUNCATE TABLE CUSTOMER_STG;
    COPY INTO CUSTOMER_STG
    FROM (
        SELECT $1, $2, $3, $4, metadata$filename, CURRENT_TIMESTAMP() FROM @CUSTOMER_STAGE
    )
    FILE_FORMAT = (TYPE = CSV, skip_header = 1)
    ON_ERROR = ABORT_STATEMENT
    purge = true;
    
    MERGE INTO CUSTOMER AS T
    USING CUSTOMER_STG AS S
    ON T.CUSTOMER_ID = S.CUSTOMER_ID
    WHEN MATCHED THEN
        UPDATE SET
            T.CUSTOMER_NAME = S.CUSTOMER_NAME,
            T.CUSTOMER_EMAIL = S.CUSTOMER_EMAIL,
            T.CUSTOMER_PHONE = S.CUSTOMER_PHONE,
            T.FILENAME = S.FILENAME,
            T.CREATED_AT = S.CREATED_AT
    WHEN NOT MATCHED THEN
        INSERT (CUSTOMER_ID, CUSTOMER_NAME, CUSTOMER_EMAIL, CUSTOMER_PHONE, FILENAME, CREATED_AT)
        VALUES (S.CUSTOMER_ID, S.CUSTOMER_NAME, S.CUSTOMER_EMAIL, S.CUSTOMER_PHONE, S.FILENAME, S.CREATED_AT);
    TRUNCATE TABLE CUSTOMER_SUMMARY;
    INSERT INTO CUSTOMER_SUMMARY (DATE, CUSTOMER_COUNT)
    SELECT DATE_TRUNC('DAY', CREATED_AT), COUNT(*)
    FROM CUSTOMER
    GROUP BY DATE_TRUNC('DAY', CREATED_AT);
END;
```

You can see the list of tasks by using below command.

```sql
SHOW TASKS LIKE 'CUSTOMER_SUMMARY_TASK';
```

In order to execute this task, your role may first need be authorized to 
execute a task. This permission can only be provided by an account administrator.

```sql
USE ROLE ACCOUNTADMIN;
GRANT EXECUTE TASK ON ACCOUNT TO ROLE SYSADMIN;
USE ROLE SYSADMIN;
```

Once you've the `EXECUTE TASK` permission, you can execute the task.

```sql
EXECUTE TASK CUSTOMER_SUMMARY_TASK;
```

Once the task is executed, you can see the history of task execution using `TASK_HISTORY` command.

```sql
SELECT * FROM TABLE(INFORMATION_SCHEMA.TASK_HISTORY());
```

In order to run the task on a schedule, you can use `ALTER TASK` command and resume the task.

```sql
ALTER TASK CUSTOMER_SUMMARY_TASK RESUME;
```

You can also change the schedule of the task using `ALTER TASK` command.

```sql
ALTER TASK CUSTOMER_SUMMARY_TASK SUSPEND;
ALTER TASK CUSTOMER_SUMMARY_TASK
SET SCHEDULE = 'USING CRON 0 0 * * * UTC';
ALTER TASK CUSTOMER_SUMMARY_TASK RESUME;
```