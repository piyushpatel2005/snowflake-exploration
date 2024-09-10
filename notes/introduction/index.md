# Snowflake Introduction

Snowflake is built natively for the cloud from the ground up.

## Snowflake Features

- Fully Managed: Snowflake is a fully managed service that eliminates the need for users to buy, install, configure or manage hardware and software themselves. Snowflake manages storage, compute, metadata, security, access control, query optimization, etc.
- Easy Ingestion: Snowflake provides easy ingestion of data from various cloud storage platforms like Amazon S3, Azure Blob Storage, Google Cloud Storage, etc. Structured and semi-structured files like CSV, JSON, Parquet, Avro, ORC, etc. can be ingested into Snowflake.
- Snowpipe: Snowpipe is a service that supports ingesting data in real-time. It allows users to ingest files as soon as they are available in the cloud storage. It is supported using cloud providers' notification services.
- Tasks: Snowflake provides support for creating tasks which can execute SQL statements, stored procedures on a recurring schedule. These tasks can also depend on other tasks which enables creation of complex workflows.
- Snowpark: Snowflake provides a developer framework called Snowpark to deploy code in a serverless manner. Snowpark is a developer framework that brings new data programmability to the cloud and makes it possible for developers, data scientists and data engineers to use Java, Scala or Python to deploy code in a serverless manner.
- Snowflake provides zero-cloning which makes copies of data without incurring additional storage costs. It allows data engineers to create new environments quickly. For example, developers can clone a production environment to create a development environment without incurring additional storage costs.
- Time Travel: Snowflake provides a feature called Time Travel which allows users to query data at different points in time. Time Travel allows users to query data at a specific point in time or a specific number of hours in the past. This feature is useful for auditing, compliance, debugging, etc.
- Snowflake replication: Snowflake provides a feature called Replication which allows users to replicate data across regions for disaster recovery, high availability, etc. Snowflake replication allows users to replicate data across regions in near real-time. This supports replication of data across cloud platforms.
- SQL Interface: Snowflake provides a SQL interface to query data and supports standard SQL features and syntax. Snowflake supports SQL commands, functions, operators, data types, etc. Snowflake also supports semi-structured data types like JSON, XML, etc.
- Governance: Snowflake provides support for governance, security, access control, auditing, compliance, etc. Snowflake provides role based access control, encryption, data masking policies to secure your data.
- Marketplace: Snowflake provides a marketplace where users can discover, share, and consume data and services. Snowflake marketplace provides data sets, data services, connectors, etc. that can be used to enrich your data and analytics.
- Partner Connect: Snowflake integrates with various partners to provide services in business intelligence, data integration, data science, etc. Snowflake provides connectors to various partners like Tableau, Looker, Informatica, Talend, etc.


## Snowflake Architecture

install, configure or manage hardware and software themselves. One of the great features of Snowflake is that storage and compute are managed seprately. This allows storage and compute to scale independently.

![Snowflake Architecture](snowflake-architecture.png "Snowflake Architecture")

Snowflake architecture can be divided into three layers: 

### 1. Storage
The storage layer is responsible for storing data in cloud storage in a compressed columnar format. Snowflake uses a proprietary storage format called Snowflake Data Format (SDF) to store data in cloud storage. SDF is a compressed columnar format that is optimized for cloud storage and processing. It stores data in a columnar format to reduce storage space and improve query performance. Snowflake also manages the structure, compression, metadata, statistics and other aspects of data storage and processing. This allows users to focus on querying and analyzing data without worrying about managing storage, indexes, partitions, etc.

### 2. Query Processing Layer

The compute layer is responsible for processing data and executing queries. Snowflake uses a separate compute layer to process the data. The compute layer is responsible for executing queries, processing data, managing resources, scaling compute clusters, etc. Snowflake uses a multi-cluster, shared data architecture to provide high availability, scalability and performance. It uses a shared-nothing architecture to distribute data and processing across multiple nodes and clusters. This allows Snowflake to scale compute resources based on the workload and provide high availability and performance.

### 3. Services Layer

The services layer is responsible for managing metadata, security, access control, query optimization, etc. Snowflake provides a web UI called Snowsight to interact with the data and manage the service. Snowsight allows users to create, manage, monitor and analyze data using a web-based interface. Snowflake also provides a developer framework called Snowpark to deploy code in a serverless manner. Snowpark is a developer framework that brings new data programmability to the cloud and makes it possible for developers, data scientists and data engineers to use Java, Scala or Python to deploy code in a serverless manner.


Snowflake capability has increased with the introduction of Snowpark. Snowpark is developer framework that brings new data programmability to the cloud and makes it possible for developers, data scientists and data engineers to use Java, Scala or Python to deploy code in a serverless manner. Snowflake comes with web UI called Snowsight.
