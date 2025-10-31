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

## Working with JSON Data

When working with JSON, you may need to parse nested or even complex data types like arrays. Snowflake provides 
convenient ways to access these elements.

### Extracting Nested Fields

If you want to extract any field from the JSON string, you can use the colon (`:`) operator.

```sql
SELECT 
    RAW_COLUMN:FIELD_NAME AS FIELD_NAME
FROM MANAGED_DB.PUBLIC.RAW_JSON_DATA;

SELECT 
    RAW_COLUMN:first_name AS customer_name
FROM MANAGED_DB.PUBLIC.RAW_JSON_DATA;
```

You can definitely convert the nested json fields into specific type by using below syntax. This is convenient if 
you want to convert from one type to another while fetching nested fields.

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

### Working with Arrays

If you have nested field which is again another complex data type like array. You can get its elements and find how 
many elements are there in the array.

```json
[
    {
        "first_name": "John",
        "last_name": "Doe",
        "age": 30
    },
    {
        "first_name": "Jane",
        "last_name": "Doe",
        "age": 25
    }
]
```

Let's assume we have `raw_json_data` dataset which includes information about housing properties along with owner 
and previous rentals. It includes past rentals in a nested field called 
`rentals`. You 
can get 
how many rentals were present at a specific house.

```sql
SELECT ARRAY_SIZE(RAW_FILE:rentals) AS num_of_rentals
    FROM MANAGED_DB.PUBLIC.RAW_JSON_DATA;
```

If you want to **extract specific nested field** like rental information, you can use index operator. The indices 
for an array 
starts from 0.

```sql
SELECT RAW_FILE:rentals[0].first_name AS first_name, RAW_FILE:rentals[0].last_name AS last_name
    FROM MANAGED_DB.PUBLIC.RAW_JSON_DATA;
```

Now, this may work only if you already know about the number of elements in each of the arrays. This is not going to 
be the case because some rows may have 1 and some rows may have more than 1 rentals in the past. If you try to use 
index and `UNION` those returned rows, you will get `null` for the rows where that index does not exist. So, it may 
not be what you actually want.

To **flatten an array**, you need to use the `FLATTEN` operator.

```sql

SELECT RAW_FILE:property_id,
    f.value:first_name AS first_name, f.value:last_name AS last_name, f.value:age AS age
    FROM MANAGED_DB.PUBLIC.RAW_JSON_DATA, TABLE(FLATTEN(RAW_FILE:rentals)) f;
```

In this query, the `FLATTEN` function would flatten the nested JSON and return the elements as separate rows. The 
`TABLE` function is used to convert the `FLATTEN` output into a table which can be used like a normal table with an 
alias `f`.

### Extracting Metadata

You can query metadata of staged parquet files. Snowflake provides below metadata columns.

| Column Name | Description                                      |
|:------------|:-------------------------------------------------|
| METADATA$FILENAME | Name of the staged file for the current row.     |
| METADATA$FILE_ROW_NUMBER | Row number for the current record in the file.   |
| METADATA$FILE_CONTENT_KEY | Checksum of the file the current row belongs to. |

```sql
SELECT METADATA$FILENAME, METADATA$FILE_ROW_NUMBER, METADATA$FILE_CONTENT_KEY
    FROM @AWS_S3.JSON_STAGE;
```

## Loading JSON Data

Once you know how to query JSON structure, you can easily use one of the known syntax to load data into your table 
by using any of the following options.

- `CTAS` or `CREATE TABLE AS` which can be used to create or replace tables

```sql
CREATE OR REPLACE TABLE MANAGED_DB.properties.customers AS 
    SELECT RAW_FILE:property_id,
        f.value:first_name AS first_name, f.value:last_name AS last_name
    FROM MANAGED_DB.PUBLIC.RAW_JSON_DATA, TABLE(FLATTEN(RAW_FILE:rentals)) f;
```

- `INSERT INTO` can be used to insert into existing tables

```sql
INSERT INTO MANAGED_DB.properties.customers
    SELECT RAW_FILE:property_id,
        f.value:first_name AS first_name, f.value:last_name AS last_name
    FROM MANAGED_DB.PUBLIC.RAW_JSON_DATA, TABLE(FLATTEN(RAW_FILE:rentals)) f;
```
