# Airbyte Tutorial

This tutorial will guide you through the process of setting up Airbyte, a data integration platform, and using it to 
replicate data from a PostgreSQL database to a Snowflake data warehouse. There are several integration platforms 
available.

## Pre-requisites

- Docker
- Python
- AWS Account
- Snowflake Account

## Why Airbyte?

Airbyte is an open-source data integration platform that helps you replicate data from different sources to 
different destinations. It is a self-hosted platform that can be deployed on your own infrastructure. Airbyte can 
handle schema changes, incremental updates, and data validation. 

- Open-source
- Nice UI
- Scalable and reliable
- Extensible

## Core Concepts

- **Source**: A source is a data connector that connects to a data source like a database, API, or file storage.
- **Destination**: A destination is a data connector that connects to a data warehouse like Snowflake, Redshift, or BigQuery.
- **Connector**: A connector is a code that connects a source to a destination. This can be push or pull based.
- **Stream**: A stream is a table or a collection of data in a source.
- **Field**: A field is a column in a stream.
- **Connection**: A connection is a configuration that connects a source to a destination. This provides information 
  on how to replicate the data between the source and the destination.

## Architecture

Airbyte has a microservices architecture. It consists of the following components:

**Airbyte DB**: This is a PostgreSQL database that stores metadata about the replication process. It stores 
information about the sources, destinations, and connections.
**Airbyte Web**: This is a web application that provides a UI to manage the replication process. You can create 
integrations, connections, and schedule replication jobs.
**Airbyte Server**: This is a server that runs the replication jobs. It pulls data from the sources and pushes it to 
the destinations.
**Airbyte Scheduler**: This is a scheduler that schedules the replication jobs. It runs the jobs at the specified 
schedule and monitors the status of the jobs. It is based on open source Temporal workflow engine.
**Airbyte Worker**: This is a worker that pulls data from the sources and pushes it to the destinations.