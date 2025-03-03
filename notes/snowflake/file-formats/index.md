# File Format Object

You can specify the file format for each `COPY INTO` command using the `file_format` option. There is also an option
to define the file format as an object just like any other database objects. Once it has been defined, you can reuse
this object for multiple `COPY INTO` commands.

```sql
CREATE OR REPLACE SCHEMA MANAGED_DB.FILE_FORMATS;
CREATE OR REPLACE FILE FORMAT MANAGED_DB.FILE_FORMATS.MY_CSV_FORMAT;
DESC FILE FORMAT MANAGED_DB.FILE_FORMATS.MY_CSV_FORMAT;
```

When you describe the file format object, you will see information about the type, delimiter, field delimiter, etc.
By default, the file format type is `CSV` and the delimiter is `,` (comma).

**Note:** You will notice that the options for CSV file format may be similar to options you see for stages. The
file format options can be used to overwrite options of a stage.

You can use this file format while copying data from a stage to a table.

```sql
COPY INTO CUSTOMER
    FROM @EXTERNAL_DB.EXTERNAL_STAGES.AWS_STAGE
    file_format = (FORMAT_NAME = MANAGED_DB.FILE_FORMATS.MY_CSV_FORMAT)
    ON_ERROR = 'CONTINUE';
```

You can also alter the file format object using the `ALTER FILE FORMAT` command.

```sql
ALTER FILE FORMAT MANAGED_DB.FILE_FORMATS.MY_CSV_FORMAT
    SET FIELD_DELIMITER = '|'
    SET FIELD_OPTIONALLY_ENCLOSED_BY = '"'
    SET SKIP_HEADER = 1;
```

If you want to overwrite the file format object for a single table, you can add those file format options in the `COPY INTO` command.

```sql
COPY INTO CUSTOMER
    FROM @EXTERNAL_DB.EXTERNAL_STAGES.AWS_STAGE
    file_format = (FORMAT_NAME = MANAGED_DB.FILE_FORMATS.MY_CSV_FORMAT FIELD_DELIMITER = ':', 
    FIELD_OPTIONALLY_ENCLOSED_BY = '"', SKIP_HEADER = 0)
    ON_ERROR = 'CONTINUE';
```

Here are some of the common options available for CSV file format.

- COMPRESSION: This is used to specify the compression type for the files.
- RECORD_DELIMITER: Specifies the delimiter for each record in the file. The default is new line `\n`.
- FIELD_DELIMITER: Specifies the delimiter for each field in the file. The default is `,`.
- SKIP_HEADER: Specifies the number of header rows to skip in the file. The default is 0.
- FIELD_OPTIONALLY_ENCLOSED_BY: Specifies the character that encloses each field in the file. The default is `NONE` 
  which means no enclosing character. This can be used to specify double quotes `"` or single quotes `'` as 
  enclosing character for string fields.
- NULL_IF: Specifies what value in a CSV file represents a `null` value. The default is `\N` which represents a 
  `null` value. You could specify multiple values using `NULL_IF = ('\N', 'NULL', 'N/A', 'null')`.
- ERROR_ON_COLUMN_COUNT_MISMATCH: Specifies whether to raise an error when the number of columns in the file does
  not match the number of columns in the table. The default is `FALSE`.

## JSON File Format

The JSON file format can be created as shown below.

```sql
CREATE OR REPLACE FILE FORMAT MANAGED_DB.FILE_FORMATS.MY_JSON_FORMAT
    TYPE = 'JSON';
    
DESC FILE FORMAT MANAGED_DB.FILE_FORMATS.MY_JSON_FORMAT;
```

If you notice, the file format options for JSON file format are different from the CSV file format. If you want to
recreate the file format, you will need to drop the existing file format and create a new one.

```sql
DROP FILE FORMAT MANAGED_DB.FILE_FORMATS.MY_JSON_FORMAT;
```

The common options for JSON file format are below.

- COMPRESSION: Specifies the compression type for the files.
- DATE_FORMAT: This defines the date string format in the JSON file. If value is not specified or is set to `AUTO`, 
  then `DATE_INPUT_FORMAT` will be used to parse the date string.
- TIME_FORMAT: This defines the time string format in the JSON file. If the value is not specified or is set to `AUTO`, 
  then `TIME_INPUT_FORMAT` will be used to parse the time string.
- NULL_IF: This specifies what values in the JSON file represent a `null` value.
- ALLOW_DUPLICATE: Whether to allow duplicate object field names or not. If there are two fields with the same name, 
  the last one will be used.

## XML File Format

The XML file format can be used to load data from XML files. Some of the options available for XML file format are
below.

- COMPRESSION: Specifies the compression type for the files.
- PRESERVE_SPACE: This option specifies whether the XML parser preserves leading and trailing spaces in element content.
- STRIP_OUTER_ELEMENT: This specifies whether to strip out the outer XML element.

## Other File Formats

There are few other file formats which are supported in Snowflake. They include ORC, PARQUET, AVRO, etc. These are
specified using the `TYPE` option. You can also define your own CUSTOM file format