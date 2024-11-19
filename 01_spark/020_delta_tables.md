# Manage delta tables

[Main Menu](../README.md)

## Check is delta table exists

```Python
dfviews = spark.sql("SHOW TABLES")
dfviews = dfviews.where(dfviews["tableName"] == "MyTable")
dfviews.show()
```

## Create a delta table

### With SQL

```SQL
CREATE TABLE IF NOT EXISTS Lakehouse.table_name (
    id int,
    code bigint,
    label string,
    mydate date
)
USING DELTA
PARTITIONED BY (mydate)
```

### With Spark

```Python
df = spark.createDataFrame(
    [
        ("Ernesto", "Guevara", "Argentina"),
        ("Maria", "Sharapova", "Russia"),
        ("Bruce", "Lee", "China"),
        ("Jack", "Ma", "China"),
    ]
).toDF("first_name", "last_name", "country")

# Create normal table
df.write \
    .format("delta") \
    .saveAsTable("myTable")

# Create partitioned table
df.repartition(F.col("country")) \
    .write \
    .partitionBy("country") \
    .format("delta") \
    .saveAsTable("myTable")

```
