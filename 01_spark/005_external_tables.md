# Create external tables

[Main Menu](../README.md)

## External table without partitions

This table will use only 1 file that is located on:

```text
Files/area/object_name
├── file.parquet
```

Files/area/object_name will be the base location folder

```SQL
CREATE EXTERNAL TABLE IF NOT EXISTS {table_name} (
    myColumn integer,
    otherColumn string
)
USING PARQUET
LOCATION "Files/area/object_name"
```

## External table with partitions

This table will use several files that is located on Files/area/object_name

Each of these files should be organized in subfolder, one level for each partition.

Imaging you are partitioning by the column country. Then the structure of sub-folders and files should be like this:

```text
Files/area/object_name
├── country=Argentina
│   └── file.parquet
├── country=China
│   └── file.parquet
└── country=Russia
    └── file.parquet
```

To create the External table

```SQL
CREATE EXTERNAL TABLE IF NOT EXISTS {table_name} (
    myColumn integer,
    otherColumn string
)
USING PARQUET
PARTITIONED BY (country string)
LOCATION "Files/area/object_name"
```

After this, you can include automatically each file to the correspond partition using:

```SQL
ALTER TABLE {table_name} RECOVER PARTITIONS
```

## using csv files

To create no partitioned external table

```SQL
CREATE EXTERNAL TABLE IF NOT EXISTS {table_name} (
    myColumn integer,
    otherColumn string
)
USING CSV
OPTIONS (header {true/false}, inferSchema {true/false}, delimiter='{delimiter}', dateFormat="dd.MM.yyyy")
LOCATION "Files/area/object_name"
```

To create partitioned external table

```SQL
CREATE EXTERNAL TABLE IF NOT EXISTS {table_name} (
    myColumn integer,
    otherColumn string
)
USING CSV
OPTIONS (header {true/false}, inferSchema {true/false}, delimiter='{delimiter}', dateFormat="dd.MM.yyyy")
PARTITIONED BY (country string)
LOCATION "Files/area/object_name"
```
