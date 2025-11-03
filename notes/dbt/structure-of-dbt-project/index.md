# Structure of DBT Projects

A DBT project is organized into a directory consisting of other directories and files with the following structure.

```
root/
├── dbt_project.yml
├── packages.yml
├── models/
│   ├── customer.sql
│   ├── orders.sql
│   └── products.sql
|   └── schema.yml
├── macros/
├── seeds/
│   ├── customer.csv
│   ├── orders.csv
│   └── products.csv
├── snapshots/
├── tests/
└── .gitignore
└── logs/
└── target/
└── dbt_packages/
```

- **dbt_project.yml**: This file is the main configuration file for the DBT project. It contains information about 
  the project, such as the name, version, and other settings. This is the file that tells dbt how to operate on your 
  project.
- **analyses/**: This directory is used to store queries which are used for data analysis. For example, you may want 
  to investigate discrepancies between two tables and want to leverage dbt capabilities like Jinja and version 
  control those queries.
- **dbt_packages/**: When you install additional dbt packages, they will be installed here. DBT ecosystem has 
  additional packages that you can use to tackle specific problems. This helps keep your code clean and DRY.
- **logs/**: This is where logs will be stored by default. You can change the location of the logs using the `log_path` variable in `dbt_project.yml`.
- **macros/**: Macros are reusable code that can be used across models. They are similar to functions in programming 
  languages which can be used multiple times in different models and are written using Jinja templates. Macros are 
  stored in this directory.
- **models/**: These are SQL files with `SELECT` statements that are used to transform the data. This is one of the 
  mandatory directories in a DBT project. The model's name indicates the name of the future table or view or it may 
  be an ephemeral model.
- **seeds/**: This is used to store look up tables in the form of CSV files. These are files which change 
  infrequently and are used for modeling data that doesn't exist in the source tables.
- **snapshots/**: This directory contains all snapshot models for your project. The snapshot feature records changes 
  to a mutable table over time. It applies type 2 SCD which helps you identify how a row in a table changes during 
  the time.
- **tests/**: This directory stores tests for your models. There will be quite a lot of tests in YAML files and 
  created as macros in SQL files. You can still have additional tests in SQL files.
- **target/**: This folder contains the compiled SQL Fiels that will be written when you run `dbt run`, `dbt 
  compile` or `dbt test` commands. You can change the directory where these files get written by changing the 
  `target_path` variable in `dbt_project.yml` file.
- **.gitignore**: This file is used to ignore files and directories that you don't want to commit to your git 
  repository.

## `dbt_project.yml` Configurations

The `dbt_project.yml` acts as a central configuration file for your DBT project. Here are some of the most important 
configurations you will find in this file.

- `name`: (Mandatory) The name of the dbt project.
- `version`: (Mandatory) The version of the dbt project.
- `config-version`: (Mandatory) Version 2 is the latest version.
- `profile`: (Mandatory) Profile is used to connect to your data platform.
- `[object]-paths`: (Optional) This is where a specific type of DBT objects are stored. Here, the object could be a model, seed, test, analysis, macro, snapshot, log, etc. For example, `model-paths` will specify the directory for models in your project.
- `target-path`: (Optional) This is where the compiled SQL files will be stored.
- `clean-targets`: (Optional) List of directories containing artifacts to be removed by the `dbt clean` command.
- `models`: (optional) This is the default configuration of the models. This can be used to specify what should happen to all models (i.e. whether they should be materialized as views or tables, etc).

## `packages.yml` file

In DBT ecosystem, packages are used to handle specific problems and can be reused and shared across multiple projects. The packages themselves are DBT projects with models and macros. You can use the models and macros defined in the package project by just importing them in your project. To access them, you first need to define them as a dependency in the `packages.yml` file. Packages can be specified in the following manner in your file.

```yml
packages:
  - package: dbt-labs/dbt_utils
    version: 1.0.0
  
  - package: dbt-labs/snowplow
    version: 0.7.0
  
  - git: "https://github.com/dbt-labs/dbt-utils.git"
    revision: 0.9.2
  
  - local: /opt/dbt/internal
```

You can install DBT packages from DBT Hub, Git repositories or local. In order ot install these dependencies, you can run the `dbt deps` command. With this command, these packages are installed in the `dbt_packages/` directory of the project.

## `profiles.yml` file

If you are using DBT cloud, you may not need this file, but if you're using DBT using DBT cli then you can setup this `profiles.yml` file  which contains the database connection to the data platform. You can store credentials as environment variables and access them through this file. You can also set up multiple profiles if you need. This is what the BigQuery profiles file looks like.

```yml
my-bigquery-db:
  target: dev
  outputs:
    dev:
      type: bigquery
      method: service-account
      project: GCP_PROJECT_ID
      dataset: DBT_DATASET_NAME
      threads: 4 # Must be a value of 1 or greater
      keyfile: /PATH/TO/BIGQUERY/keyfile.json
      OPTIONAL_CONFIG: VALUE
```

- `profile_name`: The profile name should match the name specified in the `dbt_project.yml` file.
- `target`: This is the way to specify different configurations for different environments. For instance, you would need separate datasets to work on when developing locally, but in production, you might need separate dataset. By default, the target is set to `dev`.
- `type`: The type of data platform you want to connect. This can be bigquery, snowflake, redshift, etc.
- `threads`: specifies the number of threads the DBT project will run on. This creates a DAG of links between models. This can also specify how many models it can work in parallel.

Next, depending on the database platform, you can have additional connection details.