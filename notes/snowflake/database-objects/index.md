# Managing Database Objects

Earlier in tutorial, you saw how you can select the database and schema from the UI as well as SQL. This lesson will 
cover how you can manage database objects like tables, views, and schemas in Snowflake.

When you use Snowflake UI, you will see only those database objects for which you have access. So, make sure to
select the appropriate role in order to see database objects.

You can also explore the database by simply clicking on the database and using the hyperlink to navigate to the 
database. You will see Database Details which will show the privileges and the Schemas tab will show the schemas in 
the database. Similarly, if you click on the schemas, you will see the Schema Details and the Tables tab will show 
the tables in the schema. Again, just like databse and schema objects, you can click on the table and you will see 
more details for the table with column information and a preview of the data.

## Creating Database, Schema and Table using UI

Make sure your selected role has the privileges to create database, schema, and table. In my case, I am using 
SYSADMIN role which has all the privileges.

1. Click on `Data` and then `Databases` in the left hand side menu.
2. If you have privileges, you will see `+ Database` button on the top right hand side corner. Simply click on this 
   button.
3. Provide a name for the database and optionally provide Comment and click `Create` button. 
4. You will see the database in the list of databases. You can click on the database to see the details of the 
   database. Even the icon for a database is different from the schemas and tables. Here, in the database details, 
   you can also see the option to grant privileges to the database.
5. Click on the newly created database and you will see the `+ Schemas` button on the top right hand side corner. 
   Click on this button and create new schema. Also, note that every database will have a default schema `PUBLIC`.
6. Again in the schema details page, you will see option `Create` to create various database objects including 
   tables, views, stages, etc.

## Creating Database, Schema and Table using SQL

You can alos create database using simple SQL commands which can be useful for documentation.

```sql
CREATE DATABASE IF NOT EXISTS DEMO_DB;
```

You can also create schema using SQL command.

```sql
CREATE SCHEMA IF NOT EXISTS DEMO_DB.DEMO_SCHEMA;
```

You can also create table using SQL command.

```sql
CREATE TABLE IF NOT EXISTS DEMO_DB.DEMO_SCHEMA.DEMO_TABLE
(
    ID INT,
    NAME STRING
);
```