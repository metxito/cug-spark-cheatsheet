# Creating Spark DataFrame from:

## .parquet file

To create a Spark DataFrame from a `.parquet` file stored in OneLake, you can use the `spark.read.parquet` method. Here is an example:

```python
# Path to the .parquet file in OneLake
parquet_file_path = "abfss://<container>@<account>.dfs.core.windows.net/<path>/file.parquet"

# Read the .parquet file into a DataFrame
df = spark.read.parquet(parquet_file_path)

# Show the result
df.show()
```

## SQL Query

To create a Spark DataFrame from a SQL query, you can use the `spark.sql` method. Here is an example:

```python
# Execute SQL query to create a new DataFrame
sql_query = "SELECT Name, Id FROM people WHERE Id > 1"
df = spark.sql(sql_query)

# Show the result
df.show()
```

## Static values

To create a Spark DataFrame from a SQL query, you can use the `spark.sql` method. Here is an example:

```python
# Create a temporary view from an existing DataFrame
data = [("Alice", 1), ("Bob", 2), ("Cathy", 3)]
columns = ["Name", "Id"]
df = spark.createDataFrame(data, columns)

# Show the result
df.show()
```