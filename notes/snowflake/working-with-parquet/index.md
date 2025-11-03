# Working with PARQUET data in Snowflake

In Data Engineering, one of the most commonly used columnar file format is parquet. Snowflake supports parquet as 
one of the supported file formats and provides convenient way to load and query parquet data.

## Loading Parquet Data

If you already have Parquet files in your stage, you can load them into a Snowflake table using `FILE_FORMAT`.

```sql
CREATE OR REPLACE TABLE MANAGED_DB.PUBLIC.PARQUET_DATA AS
    SELECT * FROM @MANAGED_DB.EXTERNAL_STAGE.PARQUET_STAGE
    FILE_FORMAT = (TYPE = PARQUET);
```

When the data is queried from parquet table, you will get a single column `$1` which contains the entire row data.

### Extracting Specific Columns

Similar to JSON format, you can work with parquet columns and extract individual fields using the `$1` column.

```sql
SELECT $1:FIELD_NAME FROM MANAGED_DB.PUBLIC.PARQUET_DATA;
```

The syntax for extracting `first_name` field would look like this.

```sql
SELECT $1:"first_name" FROM MANAGED_DB.PUBLIC.PARQUET_DATA;
```

You can also extract each of the fields and convert them into different data types.

```sql
SELECT $1:"first_name"::STRING AS first_name,
    $1:"last_name"::STRING AS last_name,
    DATE($1:"timestamp"::int) AS start_date
FROM MANAGED_DB.PUBLIC.PARQUET_DATA;
```

Here, the `DATE` function is used to convert the timestamp into a date type. It takes number of seconds since epoch 
time as an input.

## Loading Parquet Data

You can use `COPY INTO` command to load existing PARQUET staged data or data. You can also include file metadata in 
your `SELECT` query to include them in the final table.

```sql
COPY INTO MANAGED_DB.PUBLIC.customers
    FROM (
        SELECT $1:"first_name" AS first_name, $1:"last_name" AS last_name.
        METADATA$FILENAME AS file_name 
        FROM @MANAGED_DB.EXTERNAL_STAGE.PARQUET_STAGE
    )
    ON_ERROR = 'CONTINUE'
    FILE_FORMAT = (TYPE = PARQUET);
```