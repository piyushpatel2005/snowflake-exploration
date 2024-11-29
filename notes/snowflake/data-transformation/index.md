# Data Transformation using SQL

You saw how you can use `COPY INTO` command to load data from raw files into tables. However, you may still need to modify the data according to your choice such as adding new field as a `DATE` or calculate `totalOrderAmount` from `quantity` and `unitPrice`, etc. For these kind of situations, you can use `COPY INTO` command to load from the stage into a temporary table.

For CSV data, you can refer to each column using their column index (startign with index position 1).

```sql
COPY INTO CUSTOMER_TEMP
    FROM (SELECT s.$1, s.$2, s.$3
          FROM @EXTERNAL_DB.EXTERNAL_STAGES.AWS_STAGE s)
    FILE_FORMAT = (TYPE = CSV FIELD_OPTIONALLY_ENCLOSED_BY = '"', SKIP_HEADER = 1, FIELD_DELIMITER = ',');
```

You can also use `CREATE OR REPLACE TABLE` command to create a temporary table and load data from the stage into the table.

```sql
CREATE OR REPLACE TABLE CUSTOMER_TEMP AS
SELECT
    $1 AS CUSTOMER_ID,
    $2 AS CUSTOMER_NAME,
    $3 AS CUSTOMER_EMAIL
FROM @EXTERNAL_DB.EXTERNAL_STAGES.AWS_STAGE
(FILE_FORMAT => CSV);
```

The `COPY` command supports only few data transform functions and not all functions available with Snowflake SQL. The available functions are listed [here](https://docs.snowflake.com/en/user-guide/data-load-transform#supported-functions).

If you have more number of columns defined in the table and you want to load only few columns from the CSV file or other table, you could explicitly specify which columns to load into which specific columns of the table.

```sql
COPY INTO CUSTOMER_TEMP
    (CUSTOMER_ID, CUSTOMER_NAME, CUSTOMER_EMAIL)
    FROM (SELECT s.$1, s.$4, s.$5 FROM @EXTERNAL_DB.EXTERNAL_STAGES.AWS_STAGE s)
    FILE_FORMAT = (TYPE = CSV FIELD_OPTIONALLY_ENCLOSED_BY = '"', SKIP_HEADER = 1, FIELD_DELIMITER = ',');
```

The `CREATE TABLE` statement can also have auto-increment column. You can use `AUTOINCREMENT` keyword to create a column that auto-increments.

```sql
CREATE OR REPLACE TABLE CUSTOMER
(
    CUSTOMER_ID NUMBER AUTOINCREMENT START 1 INCREMENT 1,
    CUSTOMER_NAME VARCHAR(100),
    CUSTOMER_EMAIL VARCHAR(100)
);
```

During the data load, you don't need to load `CUSTOMER_ID` column as it will be auto-incremented.

```sql
COPY INTO CUSTOMER
    (CUSTOMER_NAME, CUSTOMER_EMAIL)
    FROM (SELECT s.$2, s.$3 FROM @EXTERNAL_DB.EXTERNAL_STAGES.AWS_STAGE s)
    FILE_FORMAT = (TYPE = CSV FIELD_OPTIONALLY_ENCLOSED_BY = '"', SKIP_HEADER = 1, FIELD_DELIMITER = ',');
```

