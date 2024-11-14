# Querying Data in Snowflake

In this lesson, you will see different ways to query data in Snowflake. The SQL dialect from Snowlake is similar to 
other SQL databases except for few differences. This lesson will show those differences.

```sql
WITH
high_prio_orders as (
  SELECT *
  FROM "SNOWFLAKE_SAMPLE_DATA"."TPCH_SF1"."ORDERS"
  WHERE O_ORDERPRIORITY IN ('1-URGENT', '2-HIGH')
)
SELECT count(*)
FROM high_prio_orders
WHERE O_ORDERDATE < '1998-01-01'
  and O_ORDERSTATUS = 'O';
```

The `SELECT` clause is used to select the columns from the table. You can use `*` to select all columns from the 
table. You can also use `DISTINCT` keyword to select unique values from the column.

```sql
SELECT * FROM "SNOWFLAKE_SAMPLE_DATA"."TPCH_SF1"."ORDERS";
SELECT DISTINCT O_ORDERPRIORITY FROM "SNOWFLAKE_SAMPLE_DATA"."TPCH_SF1"."ORDERS";
```

Snowflake also has `EXCLUDE` keyword to remove specific columns from the result set.

```sql
SELECT * EXCLUDE (O_ORDERKEY, O_CUSTKEY) FROM "SNOWFLAKE_SAMPLE_DATA"."TPCH_SF1"."ORDERS";
```

In Snowflake SQL, you can query data from multiple tables at the same time. It will bring the data from both tables. 
This gives result in the form of a Cartesian product. To avoid this, you can use `WHERE` clause 

```sql
SELECT * FROM "SNOWFLAKE_SAMPLE_DATA"."TPCH_SF1"."ORDERS", "SNOWFLAKE_SAMPLE_DATA"."TPCH_SF1"."CUSTOMER";
```

You can define inline tables in the query using `VALUES` keyword.

```sql
SELECT * FROM (VALUES (1, 'John'), (2, 'Jane') AS inline_tbl (id, name));
```

You can also use `WHERE`, `JOIN`, `GROUP BY`, `HAVING`, `ORDER BY`, `LIMIT`, and `OFFSET` clauses in the query like 
normal SQL syntax. The `HAVING` clause is used to filter the rows after the `GROUP BY` clause just like `WHERE` 
clause in normal query.

### `QUALIFY` Clause

The `QUALIFY` clause can help filter results of the window functions in the query. 

```sql
SELECT *
FROM "SNOWFLAKE_SAMPLE_DATA"."TPCH_SF1"."LINEITEM"
QUALIFY row_number()
    over(partition by L_ORDERKEY, L_LINENUMBER
         order by L_COMMITDATE desc ) = 1;
```

This query selects all rows where `row_number()` turns out to be 1.

## JOINS

Snowflake supports most of the common join types. These include `INNER JOIN`, `LEFT JOIN`, `RIGHT JOIN`, `FULL 
OUTER JOIN` 
and `CROSS JOIN`.

- `INNER JOIN`: This join returns rows when there is at least one match in both tables.
- `LEFT JOIN`: This join returns all rows from the left table and the matched rows from the right table.
- `RIGHT JOIN`: This join returns all rows from the right table and the matched rows from the left table.
- `FULL OUTER JOIN`: This join returns rows when there is a match in one of the tables.
- `CROSS JOIN`: This join returns the Cartesian product of the two tables.

## Window Functions

A window function is a SQL function which operates on  a window of rows and returns a single value for each row from 
the window. The window function is used with `OVER` clause. The `OVER` clause defines a window or a set of rows 
within the query result set. 

- `ROW_NUMBER()`: This function assigns a unique sequential integer to each row within the partition of a result set.
- `RANK()`: This function assigns a unique integer to each distinct row within the partition of a result set and it 
  will skip the ranks if there are duplicates.
- `DENSE_RANK()`: This function assigns a unique integer to each distinct row within the partition of a result set, 
  but same rank is assigned to the next values if there are duplicates.
- `NTILE()`: This function divides the result set into a specified number of groups and assigns a group number to 
  each row.
- `LAG()`: This function accesses data from a previous row in the same result set without the use of a self-join.
- `LEAD()`: This function accesses data from a subsequent row in the same result set without the use of a self-join.
- `FIRST_VALUE()`: This function returns the first value in an ordered set of values.
- `LAST_VALUE()`: This function returns the last value in an ordered set of values.
- `AVG()`: This function calculates the average of a set of values.
- `SUM()`: This function calculates the sum of a set of values.
- `COUNT()`: This function calculates the number of rows in a result set.
- `MIN()`: This function returns the minimum value in a set of values.
- `MAX()`: This function returns the maximum value in a set of values.

For defining window, you can use `PARTITION BY` clause to define the partition of the window and `ORDER BY` clause 
to order the rows in the window.

```sql
SUM (price) OVER (PARTITION BY category ORDER BY date ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING)
```

The last part defines the window frame. There are several options for defining this frame.

```sql
ROWS BETWEEN M PRECEDING AND N FOLLOWING
ROWS BETWEEN UNBOUNDED PRECEDING AND M [PRECEDING | FOLLOWING]
ROWS BETWEEN CURRENT ROW AND N FOLLOWING
```