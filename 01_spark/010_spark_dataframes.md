# Work with Spark DataFrames

[Main Menu](../index.md)

## Queries

```Python

#Show all entries but only the firstName column
df.select("firstName").show()

#Show all entries but only the firstName and lastName columns
df.select("firstName", "lastName") \
    .show()

#Show all entries but firstName, age and type columns
df.select("firstName", "age", explode("phoneNumber").alias("contactInfo")) \
    .select("contactInfo.type", "firstName", "age") \
    .show()

#Show all entries in firstName and age, add 1 to the entries of age
df.select(df["firstName"], df["age"]+ 1) \
    .show()

#Show all entries where age >24
df.select(df["age"] > 24).show()
```

## Queries : WHEN

```Python
from pyspark.sql import functions as F

#Show firstName and 0 or 1 depending on age >30
df.select("firstName", F.when(df.age > 30, 1).otherwise(0)) \
    .show()

#Show firstName if in the given options
df[df.firstName.isin("Jane", "Boris")] \
    .collect()
```

## Queries : LIKE

```Python
df.select("firstName", df.lastName.like("Smith")) \
    .show()
```

## Queries : Startswith Endswith

## Filter

```Python
df.filter(df["age"] > 24).show()
df.where(df["age"] > 24).show()
```

## Remove duplicates

```Python
 df = df.dropDuplicates()
```

## Create Spark DataFrame from .parquet file

To create a Spark DataFrame from a `.parquet` file stored in OneLake, you can use the `spark.read.parquet` method. Here is an example:

```python
# Path to the .parquet file in OneLake
parquet_file_path = "abfss://<container>@<account>.dfs.core.windows.net/<path>/file.parquet"

# Read the .parquet file into a DataFrame
df = spark.read.parquet(parquet_file_path)

# Show the result
df.show()
```

## Create Spark DataFrame from .json file

To create a Spark DataFrame from a `.json` file stored in OneLake, you can use the `spark.read.json` method. Here is an example:

```python
# Path to the .parquet file in OneLake
json_file_path = "abfss://<container>@<account>.dfs.core.windows.net/<path>/file.json"

# Read the .parquet file into a DataFrame
df =  spark.read.json(json_file_path)

# Show the result
df.show()
```

## Create Spark DataFrame from SQL Query

To create a Spark DataFrame from a SQL query, you can use the `spark.sql` method. Here is an example:

```python
# Execute SQL query to create a new DataFrame
sql_query = "SELECT Name, Id FROM people WHERE Id > 1"
df = spark.sql(sql_query)

# Show the result
df.show()
```

## Create Spark DataFrame from Static values

To create a Spark DataFrame from a SQL query, you can use the `spark.sql` method. Here is an example:

```python
# Create a temporary view from an existing DataFrame
data = [("Alice", 1), ("Bob", 2), ("Cathy", 3)]
columns = ["Name", "Id"]
df = spark.createDataFrame(data, columns)

# Show the result
df.show()
```
