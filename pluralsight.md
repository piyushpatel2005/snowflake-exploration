# Snowflake Core

## Required skills for exam
- Data loading and transformation
- Virtual warehouse performance and concurrency
- DDL, DML queries
- Using structured, semi-structured and unstructured data
- Data cloning and time travel
- Data sharing
- Snowflake account structure and management

Passing requirements
- 115 mins with total 100 questions
- Passing score requires 750+ score with maximum of 1000 score
- Snowflake Cloud Data platform features and architecture: 20-25%
- Account access and security: 20-25%
- Performance Concepts: 10-15%
- Data loading and unloading: 5-10%
- Data transformations: 20-25%
- Data protection and data sharing: 5-10%

## UI Intro
- First thing -> worksheets -> purpose
- Dashboards -> 
- Streamlit -> deploy data apps without web dev experience
- Apps
- Marketplace - Health check
- Data
- Activity: view history of all queries executed
- Admin: command center for administering snowflake experience.

## SnowSQL Demo

```shell
snowsql -v
snowsql -a [ACCOUNT] - u [user_id] -d [database] -s [schema] -w [warehouse]
!help # in snowsql to get help
!exit # in snowsql to exit
type config # you can define multiple configurations
snowsql -c myconfig
snowsql -c myconfig -f query.sql
```



