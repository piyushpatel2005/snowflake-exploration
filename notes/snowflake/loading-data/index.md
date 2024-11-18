# How to Load Data into Snowflake

There are two main methods for loading data into our Snowflake tables.

1. Bulk loading: This is the most commonly used method for loading data. It loads batch of data in single statement. 
   This also uses a concept called stages which is a place where the data is stored before loading into the table. 
   The `COPY` command is used to load data from the stage into the table. You can also perform minor transformations 
   during this bulk load operation.
2. Continuous loading: This is used when you want to load data continuously. This is used when you have a stream of data 
   coming in and you want to load it into the table as soon as it arrives. This is usually used to load small 
   volumnes of data. This uses serverless feature of Snowflake called Snowpipe.