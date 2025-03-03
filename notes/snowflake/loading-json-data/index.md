# Load JSON Data into Snowflake

Any data processing job can be divided into following steps.
1. Create a stage object for storing the files.
2. Load raw data using `VARIANT` type.
3. Analyze the data and parse required fields.
4. Flatten the data and load it into a table.

## 1. Create Stage object.

First you need to create a stage object to store the JSON files. In my case, I have several JSON files located in 
below location.

```sql
CREATE OR REPLACE stage MANAGED.DB.EXTERNAL_STAGE.JSON_STAGE
    URL = 's3://snowflake-samples-data/json/';
```

Next, create a file format for your JSON data.

```sql
CREATE OR REPLACE FILE FORMAT MANAGED.DB.FILE_FORMATS.JSON_FORMAT
    TYPE = JSON;
```

## 2. Load Raw Data

First you need to load the data in the raw format without actually parsing each fields of the JSON file. These 
tables are usually called `RAW` tables.

```sql
CREATE OR REPLACE TABLE MANAGED_DB.PUBLIC.RAW_JSON_DATA (
    FROM @MANAGED_DB.EXTERNAL_STAGE.JSON_STAGE
    FILE_FORMAT = MANAGED_DB.FILE_FORMATS.JSON_FORMAT
    FILES = ('*.json');
```

Next, you can check the data as it is stored.

```sql
SELECT * FROM MANAGED_DB.PUBLIC.RAW_JSON_DATA;
```

If you want to extract any field from the JSON string, you can use the colon (`:`) operator.

```sql
SELECT 
    RAW_COLUMN:FIELD_NAME AS FIELD_NAME
FROM MANAGED_DB.PUBLIC.RAW_JSON_DATA;

SELECT 
    RAW_COLUMN:first_name AS customer_name
FROM MANAGED_DB.PUBLIC.RAW_JSON_DATA;
```

You can definitely convert the nested json fields into specific type by using below syntax.

```sql
SELECT 
    RAW_COLUMN:FIELD_NAME::FIELD_TYPE AS FIELD_NAME
FROM MANAGED_DB.PUBLIC.RAW_JSON_DATA;

SELECT 
    RAW_COLUMN:first_name::STRING AS customer_name
FROM MANAGED_DB.PUBLIC.RAW_JSON_DATA;
SELECT RAW_COLUMN:age::int AS age
    FROM MANAGED_DB.PUBLIC.RAW_JSON_DATA;
```

If the name field is another nested JSON structure as shown below, you can extract the fields like below.

```json
{
    "name": {
        "first_name": "John",
        "last_name": "Doe"
    },
    "age": 30
}
```

```sql
SELECT RAW_COLUMN:name.first_name AS first_name, RAW_COLUMN:age:int AS age
    FROM MANAGED_DB.PUBLIC.RAW_JSON_DATA;
```