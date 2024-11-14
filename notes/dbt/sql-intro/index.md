# Introduction to SQL

If you're new to SQL, this tutorial will help you get started with SQL. SQL is essential skill for any data 
engineering or data analytics related roles. SQL stands for Structured Query Language. It is a standard language for 
interacting with relational databases. SQL is used to query, insert, update, and delete data from databases. Some of 
the features of SQL are below. This tutorial covers Snowflake SQL syntax so some of the commands might be different 
in other database dialects.

1. SQL is relatively easy to learn and use. It's also easy to read.
2. It is supported by most of the RDBMS systems like MySQL, PostgreSQL, SQL Server, Oracle, etc.
3. SQL is declarative language. You specify what you want to do, not how to do it.

Relational Database is a database that stores data in tables. It consists of databses which contain tables organized 
in schema. Tables store information in the form of rows of data where each row is a record. A table may contain 
several columns, each representing a different field. These are items like customer name, address, phone number, etc.

## SQL Commands

SQL commands are used to interact with databases. You can create, read, update, and delete data using SQL commands. 
The commands are categorized into these three categories.

1. **DDL (Data Definition Language)**: DDL commands are used to define the structure of the database. Some of the DDL 
   commands are `CREATE`, `ALTER`, `DROP`, `TRUNCATE`, etc.
2. **DML (Data Manipulation Language)**: DML commands are used to manipulate the data in the database. Some of the DML 
   commands are `SELECT`, `INSERT`, `UPDATE`, `DELETE`, etc.
3. **DCL (Data Control Language)**: DCL commands are used to control the access to the data in the database. Some of the 
   DCL commands are `GRANT`, `REVOKE`, etc.

### Create Table

The `CREATE TABLE` command is used to create a new table in the database. The syntax of the `CREATE TABLE` command 
is as follows. You would specify the table name and columns along with data types for each column.

```sql
CREATE TABLE table_name
(
    column1 datatype,
    column2 datatype,
    column3 datatype,
    ...
);
```

You can also create a view in the database. A view is a virtual table that is based on the result of a SQL query. It 
does not store any data in the database but displays data from the existing tables depending on the query.

```sql
CREATE VIEW view_name AS
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```

The tables and views are stored in a schema and the schema is logical container for the database objects. You can 
create database and schema using below commands.

```sql
CREATE DATABASE database_name;
CREATE SCHEMA database_name.schema_name;
```

### Users and Roles

Databases also have a concept or users and roles. These are used to control access to the database objects. A role 
is used to group associated privileges and a role is assigned to users to grant users the same set of permissions. You 
can 
use `GRANT` and `REVOKE` commands to provide or revoke access to the database objects.

```sql
CREATE ROLE role_name;
CREATE USER user_name;
GRANT ROLE role_name TO USER user_name;
```

## DDL Commands

- `CREATE`: Create a new table, view, or database.
- `DROP`: Used to delete the specified database, table, view, or index.
- `ALTER`: Used to modify the structure of the database objects.
- `DESC`: Used to describe the structure of the specific database object.
- `SHOW`: Used to show the list of databases, tables, views, etc.
- `USE`: Used to switch to a specific database, schema or object.

## DML Commands

- `INSERT`: Insert rows into a table.
- `SELECT`: Retrieve data from one or more tables. This can be used to query data from tables and you can also use 
  grouping or filtering operation in your query.
- `UPDATE`: Update existing records in a table.
- `DELETE`: Removes specific rows from a table.
- `MERGE`: Insert, Update or Delete data from a table based on a condition.
- `TRUNCATE`: Remove all rows from a table while keeping the definition and privileges.
- `COPY INTO`: Load data from file(s) in the stage location into a table.
- `PUT`: Upload a local file into a Snowflake stage.
- `GET`: Download a file from a Snowflake stage to a local file.
- `REMOVE`: Remove a file from a Snowflake stage.
- `LIST`: List the files in a Snowflake stage.

## DCL Commands

- `GRANT`: Provides privileges to the user or role.
- `REVOKE`: Removes privileges from the user or role.
- `SHOW GRANTS`: Shows the privileges granted to the user or role.

In addition to these commands, there are few other commands that are used to manage transactions, sessions, etc.

- `BEGIN`: Start a new transaction.
- `COMMIT`: Save the changes made in the transaction. This is used to make multiple changes as a single transaction.
- `ROLLBACK`: Rollback the changes made in the transaction. This is like an undo operation applied after the start 
  of the transaction.
- `SET`: Set the session parameters like timezone, role, etc.
- `UNSET`: Unset the session parameters.

