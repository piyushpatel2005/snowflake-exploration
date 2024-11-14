# Snowflake Setup

In order to follow along with this tutorials, you need to have a Snowflake account. You can sign up for a free trial 
account at [Snowflake](https://www.signup.snowflake.com/). Snowflake provides a 30-day free trial account with $400 
credits to get started. This should be more than enough to follow these tutorials and to learn Snowflake and DBT.

When you sign up for a Snowflake account, the user you specified to login will have default role of `ACCOUNTADMIN`. 
This is the superuser role and has all the privileges to manage the Snowflake account. It's not recommended to use 
this role for day-to-day activities. You should create a new role and user for your day-to-day activities to follow 
the principle of least privileges.

By default, you will have two databases:
1. `SNOWFLAKE`: This is where the metadata about the Snowflake account is stored.
2. `SNOWFLAKE_SAMPLE_DATA`: This is a sample database that contains sample data for you to play around with and to 
   learn SQL syntax with Snowflake data.

The initial user can create new users and roles using `ACCOUNTADMIN` rike, `ACCOUNTADMIN` role has been granted the 
`USERADMIN` role.

To create new pipelines using DBT, you should create a new role for the pipelines.

```sql
USE ROLE USERADMIN;
CREATE ROLE DBT_ROLE
    COMMENT = 'Role for DBT pipelines';
```

You can assign this role to a developer or group of developes using below command.

```sql
GRANT ROLE DBT_ROLE TO USER <user_name>;
```

## Database setup

Snowflake by default provides `SYSADMIN` role in order to manage structure of databases and warehouse settings. You 
should provide only the required privileges to the role that is required in order to do the job. For this case, you 
can assign privileges on WAREHOUSE and DATABASE creation privileges to the `DBT_ROLE`.

```sql
USE ROLE SYSADMIN;
GRANT CREATE DATABASE ON ACCOUNT TO ROLE DBT_ROLE;
GRANT USAGE ON WAREHOUSE COMPUTE_WH TO ROLE DBT_ROLE;
```

Now, you can verify the privileges by trying to create a database.

```sql
USE ROLE DBT_ROLE;
CREATE DATABASE DBT_SNOWFLAKE;
```

You can explore this new database on the left side of the Snowflake UI. You may have to refresh the UI to see the 
new DB. By default, it contains two schemas: `PUBLIC` and `INFORMATION_SCHEMA`. The `PUBLIC` schema is where you
can create your tables and views. The `INFORMATION_SCHEMA` schema contains metadata about the database objects.

## Create new user

Now, you can create a new user for the developer who will be using DBT_ROLE to create data pipelines.

```sql
USE ROLE USERADMIN;
CREATE USER IF NOT EXISTS DBT_USER
    COMMENT = 'User for DBT pipelines'
    PASSWORD = 'password'
    DEFAULT_WAREHOUSE = 'COMPUTE_WH'
    DEFAULT_ROLE = 'DBT_ROLE';
GRANT ROLE DBT_ROLE TO USER DBT_USER;
USE ROLE DBT_ROLE;
```

