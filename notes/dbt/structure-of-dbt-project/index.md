# Structure of DBT Projects

A DBT project is organized into a directory consisting of other directories and files with the following structure.

```
root/
├── dbt_project.yml
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
- 