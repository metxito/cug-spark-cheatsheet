# Adding and Deleting Partitions in Delta Lake tables

[Main Menu](../README.md)

This post shows you how to add and delete partitions in Delta tables. You’ll learn why Delta Lake does not have ADD PARTITION and DROP PARTITION commands as Hive does and how Delta Lake treats Hive-style partitions differently.

Let’s start by creating a partitioned Delta table and then see how to add and remove partitions

Based on [Post](https://delta.io/blog/2023-01-18-add-remove-partition-delta-lake/)

## Create a table with partitions

```Python
# Create a data frame
df = spark.createDataFrame(
    [
        ("Ernesto", "Guevara", "Argentina"),
        ("Maria", "Sharapova", "Russia"),
        ("Bruce", "Lee", "China"),
        ("Jack", "Ma", "China"),
    ]
).toDF("first_name", "last_name", "country")

# Write the DataFrame out to a Delta table called country_people
df.repartition("country") \
    .write \
    .partitionBy("country") \
    .format("delta") \
    .saveAsTable("country_people")
```

Here are the contents of the Delta table:

```text
spark-warehouse/country_people
├── _delta_log
│   └── 00000000000000000000.json
├── country=Argentina
│   └── part-00000-03ceafc8-b9b5-4309-8457-6e50814aaa8b.c000.snappy.parquet
├── country=China
│   └── part-00000-9a8d67fa-c23d-41a4-b570-a45405f9ad78.c000.snappy.parquet
└── country=Russia
    └── part-00000-c49ca623-ea69-4088-8d85-c7c2de30cc28.c000.snappy.parquet
```

## Add a new partition

```Python
#Let’s create another DataFrame with some individuals from Colombia.
df = spark.createDataFrame(
    [
        ("Orlando", "Cabrera", "Colombia"),
        ("Carlos", "Vives", "Colombia"),
    ]
).toDF("first_name", "last_name", "country")

# Append this DataFrame to the partitioned Delta table:
df.repartition("country") \
    .write.mode("append") \
    .partitionBy("country") \
    .format("delta") \
    .saveAsTable("country_people")
```

Here are the contents of the Delta table:

```text
spark-warehouse/country_people
├── _delta_log
│   ├── 00000000000000000000.json
│   └── 00000000000000000001.json
├── country=Argentina
│   └── part-00000-03ceafc8-b9b5-4309-8457-6e50814aaa8b.c000.snappy.parquet
├── country=China
│   └── part-00000-9a8d67fa-c23d-41a4-b570-a45405f9ad78.c000.snappy.parquet
├── country=Colombia
│   └── part-00000-7e3d3d49-39e9-4eb2-ab92-22a485291f91.c000.snappy.parquet
└── country=Russia
    └── part-00000-c49ca623-ea69-4088-8d85-c7c2de30cc28.c000.snappy.parquet
```

## Remove Partitions

```Python
# Delete all the rows with individuals from Argentina.
dt = delta.DeltaTable.forName(spark, "country_people")
dt.delete(F.col("country") == "Argentina")
```

You need to run vacuum twice to completely remove the Argentina partition. The first vacuum run deletes the files with Argentina data, and the Argentina directory becomes empty. The second vacuum run deletes the empty Argentina directory. You don’t normally have to run vacuum twice for all changes to take effect, but this is a special edge case. See this [Blog Post](https://delta.io/blog/2023-01-03-delta-lake-vacuum-command/) to learn more about the vacuum command.

```Python
spark.conf.set("spark.databricks.delta.retentionDurationCheck.enabled", "false")

spark.sql("VACUUM country_people RETAIN 0 HOURS")
spark.sql("VACUUM country_people RETAIN 0 HOURS")
```

Here is the contents of the filesystem and make sure that the Argentina partition was removed.

```text
spark-warehouse/country_people
├── _delta_log
│   ├── 00000000000000000000.json
│   ├── 00000000000000000001.json
│   └── 00000000000000000002.json
├── country=China
│   └── part-00000-9a8d67fa-c23d-41a4-b570-a45405f9ad78.c000.snappy.parquet
├── country=Colombia
│   └── part-00000-7e3d3d49-39e9-4eb2-ab92-22a485291f91.c000.snappy.parquet
└── country=Russia
    └── part-00000-c49ca623-ea69-4088-8d85-c7c2de30cc28.c000.snappy.parquet
```
