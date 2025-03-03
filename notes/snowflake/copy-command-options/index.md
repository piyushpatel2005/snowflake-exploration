# Options for COPY Command

The `COPY` command supports multiple options to configure the copy process. Based on the options specified, the copy 
command will change its behavior.

## ON_ERROR

The `ON_ERROR` option allows you to specify the behavior when an error occurs during the copy process. Valid values 
are `CONTINUE`, `ABORT_STATEMENT`, `SKIP_FILE`.

- `CONTINUE`: Continue the copy process even if an error occurs. This will produce a status of `PARTIALLY_LOADED`.
- `ABORT_STATEMENT`: Abort the copy process and stop the entire operation. This is the default behavior of the `COPY` 
  command.
- `SKIP_FILE`: Skip the file which produced the error and continue to the next file.
- `SKIP_FILE_<num>`: Skip a file when the number of error rows found in the file is equal to or greater than `<num>`.
  If we are below this number, the file is processed normally except those error rows.
- `SKIP_FILE_<num>%`: Skip a file when the number of error rows found in the file is greater than or equal to 
  `<num>%` of the total number of rows.

```sql
COPY INTO CUSTOMER
    FROM @EXTERNAL_DB.EXTERNAL_STAGES.AWS_STAGE
    file_format = (type = 'csv', field_delimiter = ',', skip_header = 1)
    ON_ERROR = 'CONTINUE';
```

## VALIDATION_MODE

The `VALIDATION_MODE` option allows you to specify the validation mode for the copy process. Valid values are 
RETURN_ERRORS, RETURN_n_ROWS. When we have set the `VALIDATION_MODE`, it will not perform the copy process but it 
will only verify the data.

- `RETURN_ERRORS`: Return rows that failed validation. If there are no errors, it will not return any rows.
- `RETURN_n_ROWS`: Return the first n rows and verify the data copy process. Again, it will not actually copy the data.

```sql
COPY INTO CUSTOMER
    FROM @EXTERNAL_DB.EXTERNAL_STAGES.AWS_STAGE
    file_format = (type = 'csv', field_delimiter = ',', skip_header = 1)
    VALIDATION_MODE = 'RETURN_ERRORS';
    
COPY INTO CUSTOMER
    FROM @EXTERNAL_DB.EXTERNAL_STAGES.AWS_STAGE
    file_format = (type = 'csv', field_delimiter = ',', skip_header = 1)
    VALIDATION_MODE = 'RETURN_5_ROWS';
```

When there are errors with validation in the `COPY INTO` command, the rejected rows will be available in the output in 
`REJECTED_RECORD` column. You can retrieve those rows for the last query using below SQL command.

```sql
SELECT * FROM TABLE (RESULT_SCAN(LAST_QUERY_ID()));
```

You can use this result to correctly populate those errored rows.

```sql
INSERT INTO rejected
SELECT rejected_record FROM TABLE (RESULT_SCAN(LAST_QUERY_ID()));
```

If errors occur in the `COPY INTO` command with `ON_ERROR=CONTINUE`, the rejected rows can be retrieved using below SQL.

```sql
SELECT * FROM TABLE (VALIDATE(<table_name>, job_id => '_last'));
```

## RETURN_FAILED_ONLY

This option allows you to specify whether to return only the files that failed during the COPY command. This is
usually used with `ON_ERROR=CONTINUE` so that it will only output the file names which have had some errors while
loading the data. The default value is `FALSE`.

```sql
COPY INTO CUSTOMER
    FROM @EXTERNAL_DB.EXTERNAL_STAGES.AWS_STAGE
    file_format = (type = 'csv', field_delimiter = ',', skip_header = 1)
    ON_ERROR = 'CONTINUE'
    RETURN_FAILED_ONLY = TRUE;
```

## SIZE_LIMIT

This specifies the maximum size of the file (in bytes) to be copied using `COPY INTO` command. At least one file will 
always be 
loaded before checking the size limit. If after the first file, this limit is exceeded, the next file will not be 
loaded.

```sql
COPY INTO CUSTOMER
    FROM @EXTERNAL_DB.EXTERNAL_STAGES.AWS_STAGE
    file_format = (type = 'csv', field_delimiter = ',', skip_header = 1)
    SIZE_LIMIT = 10000; -- 10KB limit
```

## TRUNCATECOLUMNS

This option specifies whether a column value should be truncated or not. If we have a table with a column of type 
VARCHAR(100), if the column value is more than 100 characters, the value will be truncated. By default, this option 
is `FALSE`. If this option is set to `FALSE` and we have a value with more than 100 characters, it will raise an 
error.

```sql
COPY INTO CUSTOMER
    FROM @EXTERNAL_DB.EXTERNAL_STAGES.AWS_STAGE
    file_format = (type = 'csv', field_delimiter = ',', skip_header = 1)
    TRUNCATECOLUMNS = TRUE;
```

## FORCE

This option forces the loading of files which have already been loaded previously. The default value is `FALSE`. If 
it's set to `FALSE`, then the file which has already been loaded and has not changed will not be loaded again. This 
can cause duplicates in the target table.

```sql
COPY INTO CUSTOMER
    FROM @EXTERNAL_DB.EXTERNAL_STAGES.AWS_STAGE
    file_format = (type = 'csv', field_delimiter = ',', skip_header = 1)
    FORCE = TRUE;
```

