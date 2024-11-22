# Stages 

This is the location of the data files from where data can be loaded into tables. The stages have properties. These properties can be set to control the access to the stage. 

## Types of Stages

There are two types of stages:

1. Internal Stage
2. External Stage

### Internal Stage

Internal stage is a stage that's maintained by Snowflake itself.

### External Stage

This is the most common type of stage. These includes AWS S3, Azure Blob Storage, Google Cloud Storage, and other cloud storage services. This is created within a database schema using a `CREATE` statement so this is a database object. If external stage is in a different platform than the one selected for our account, this can incur additional data transfer costs because of the egress charges from the cloud provider.

## Working with Stages

This section covers the Snowflake SQL commands to work with stages.

### Creating a Stage

You can create a new stage using `CREATE STAGE` command. Usually it's best practice to keep stages in a separate database schema.

```sql
CREATE OR REPLACE DATABASE EXTERNAL_DB;
CREATE OR REPLACE SCHEMA EXTERNAL_STAGES;

CREATE OR REPLACE STAGE EXTERNAL_DB.EXTERNAL_STAGES.AWS_STAGE
    URL = 's3://bucket-name/path'
    CREDENTIALS = (AWS_KEY_ID = 'aws-key-id' AWS_SECRET_KEY='aws-secret-key');
```

As you can see, you can pass the credentials to the stage using the `CREDENTIALS` parameter above. Now, if you describe the stage, you can see the properties associated with the stage.

```sql
DESC STAGE EXTERNAL_DB.EXTERNAL_STAGES.AWS_STAGE;
```

Some of the useful properties you will see include the `STAGE_LOCATION`, `FILE_DELIMITER`, `RECORD_DELIMITER`, `SKIP_HEADER`, `FIELD_OPTIONALLY_ENCLOSED_BY`, `CREDENTIAL_REFERENCE`, `CREDENTIALS`, etc.

### Altering a Stage
If you ever change your credentials or if you want to change any of the properties of the stage once you've created it, you can use the `ALTER STAGE` command.

```sql
ALTER STAGE EXTERNAL_DB.EXTERNAL_STAGES.AWS_STAGE 
    SET CREDENTIALS = (
        aws_key_id = 'aws-key-id',
        aws_secret_key = 'aws-secret'
    );
```

### Listing Stages
If you want to create a stage for publicly accessible data, you can create a stage without credentials. In order to list a stage, you can use `@` symbol followed by the stage name.

```sql
-- public access stage area
CREATE OR REPLACE STAGE EXTERNAL_DB.EXTERNAL_STAGES.PUBLIC_STAGE
    URL = 's3://bucketsnowflake3';

LIST @PUBLIC_STAGE;
```

## Copying Data from Stage

