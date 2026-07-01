
#### Streaming is Continuous processing of incoming data in real time or near real time 

#### Old Streaming in Spark
- Instead of processioning one record at a time , Spark groups the incoming data into small time intervals ( eg. 1 second ) and treats each as a mini batch job
- Key Concepts in DStreams:
	- Uses Sparks Batch engine under the hood
	- Easy to implement and scale
	- Better fault tolerant

#### Structured Streaming
-  STREAM = UNBOUNDED TABLE
- A stream is treated as a table that is constantly growing
- New data is like new rows being appended to the table
- It allows you to query this growing table as if it was a static table
- It keeps updating the results as new data arrives


## Stateless Transformations

- This is similar to the Narrow Transformations
- ![](../attachments/Pasted%20image%2020260628195952.png)

- When the micro batch / stream is processed in Stateless Transformation, It does not refer to the last stream of data as it is not required
- It only needs to append the result

## Stateful Transformation

- Similar to Wide Transformation
- 
![](../attachments/Pasted%20image%2020260628201339.png)

- Spark needs to remember the output of the previous data for the Updated 
- ![](../attachments/Pasted%20image%2020260628201806.png)
- The same type of data will go to the same partition as before
- It needs to remember the previous state of the memory


## Output Modes

- A way to define how the output table will behave based on the micro batch / Stream

- #### Append
	-  Append will only work with Stateless Transformations
	- Append will only process the New records and does not save the State of the records 
	- It will only write the new data each time a new Stream comes

```python 
my_schema = StructType([
					StructField("order_id", StringType(), True),
					StructField("timestamp", StringType(), True),
					StructField("customer", StructType([
						StructField("customer_id", IntegerType(), True),
						StructField("name", StringType(), True),
						StructField("email", StringType(), True),
						
						StructField("address", StructType([
							StructField("city", StringType(), True),
							StructField("postal_code", StringType(), True),
							StructField("country", StringType(), True)
						]), True)
					]), True),

					StructField("items", ArrayType(
						StructType([		
							StructField("item_id", StringType(), True),	
							StructField("product_name", StringType(), True),
							StructField("quantity", IntegerType(), True),
							StructField("price", DoubleType(), True)
						])
					), True),
					
					StructField("payment", StructType([
						StructField("method", StringType(), True),
						StructField("transaction_id", StringType(), True)
					]), True),
					
					StructField("metadata", ArrayType(
						StructType([
							StructField("key", StringType(), True),
							StructField("value", StringType(), True)
						])
					), True)
			])

df = spark.readStream.format("json") \
		.option("multiline", True) \
		.schema(my_schema) \
		.load("/Volumes/workspace/streaming_learning/streaming_volume/jsonfile")

df = df.select(
		"items",
		"metadata",
		F.col("customer.customer_id").alias("customer_id"),
		F.col("customer.email").alias("customer_email"),
		F.col("customer.name").alias("customer_name"),
		F.col("customer.address.city").alias("customer_city"),
		F.col("customer.address.city").alias("customer_country"),
		F.col("customer.address.postal_code").alias("customer_postal_code")
)

df = df.withColumn("items", F.explode("items"))
df = df.withColumn("metadata", F.explode("metadata"))

df = df.select(
		"*",
		F.col("items.item_id").alias("item_id"),
		F.col("items.product_name").alias("product_name"),
		F.col("metadata.key").alias("key"),
		F.col("metadata.value").alias("value"),
)

df = df.drop("metadata").drop("items")

df.writeStream.format("delta") \
			.outputMode("append") \
			.option("path", "/Volumes/workspace/streaming_learning/streaming_volume/jsonsink/data") \
			.option("checkpointLocation", "/Volumes/workspace/streaming_learning/streaming_volume/jsonsink/checkpoint") \
			.trigger(once=True) \
			.start()
```


## Idem-potency

- Spark streaming wont process the already processed data again
- Even if the same file/row/stream is sent again it wont be process
- This is done using Checkpoint Location. 

## Checkpoint 

- Checkpoint is a folder that is defined while writing the stream 
- ![](../attachments/Pasted%20image%2020260629163131.png)
- ![158](../attachments/Pasted%20image%2020260629163143.png)
- Checkpoint will store the streaming query id.
- #### Sources 
	- **Purpose:** Tracks which input files have already been discovered and processed.
	- This folder is mainly used with **file-based streaming sources** (ADLS, S3, local files, etc.).
	- It stores metadata about files Spark has already processed, such as:
		- File path
		- File modification time
		- File identifier
	- This prevents Spark from reading the same file again after a restart.
	
- #### Offsets
	- **Purpose:** Stores the starting and ending offsets (or positions) processed for every micro-batch.
	- Think of an **offset** as a bookmark.
	- Different sources have different meanings:
		- Kafka → Offset = message number
		- Event Hubs → Offset = event position
		- Files → Offset = list of files discovered for that batch

- #### Commits
	- **Purpose:** Records which micro-batches have been successfully completed.
	- Think of it as a **"Done"** marker.
	- Example
		- Suppose Spark processes
		- Batch 0	
			- Read data
			- Write to Delta
			- Success
		- Spark creates
			`commits/    0`
		- Next,
		- Batch 1
			- Read data
			- Write data
			- Success
		- Spark creates
			`commits/    1`

	- Now suppose Spark crashes while processing Batch 2.
	- You'll have
			`offsets/    
			 `2`
			`commits/    
			 `0`   
			 `1`
	- Notice:
			`offsets/`
			`2` 
			`existscommits/`
			`2` does NOT exist
	- When Spark restarts, it sees:
        	`"Batch 2 was started but never committed."`

	- So it safely reprocesses Batch 2.
	- This is one of the mechanisms that helps Spark achieve **exactly-once processing** .

## Triggers

- Triggers are the action to run the query
- #### Default
	- It will wait until the previous batch is processed
	- Batch 1 completed -> Batch 2 Started
- #### Processing Time
	- `.trigger(processingTime="10 seconds")`
	- This trigger will check the source for every 10 seconds and if new data is added it will process the data
- ####  Once
	- All the data in source will be processed once and then it will be stopped
	- Behaves like a batch
	- It will process all the data in the source as 1 batch
- #### Available Now
	- This also will run once and but will process the entire data in the source as micro batches
	- Better version than once
- #### Continuous
	- This is a new trigger and does not support all 
	- It is only for append mode
	- This will process the data continuously record by record
	- `trigger(continous = '1 second)`
	- The parameter `continuous` is for the checkpoint locations

## Archiving Source files

- You can create an Archive directory and move all the processed file in the Archived location
- Even if we re-upload the processed file it will not go it the Archive because archive only takes the processed file
- Spark does not reprocessing  the same file

```python
df = spark.readStream.format("json") \
		.option("multiline", True) \
		.schema(my_schema) \
		.option("cleanSource", "archive") \
		.option("sourceArchiveDir", "path_of_the_archive") \
		.load("source_path")
```

- The source folder will be empty once the file is moved to archive
- The source folder might have already processed file
- ![](../attachments/Pasted%20image%2020260629200426.png)
- The new file is sent to archive only if the next trigger / new batch is done


## Modes 

- #### Append
	- Append will only work with Stateless Transformations
	- Append will only process the New records and does not save the State of the records 
	- It will only write the new data each time a new Stream comes

- #### Complete

	- ![](../attachments/Pasted%20image%2020260630195818.png)
	- **Batch 1 arrives.**
		1. Spark processes Batch 1.
		2. The **state** (not just the output) is stored in the **State Store**.
		3. The State Store keeps the state in memory (when possible) for fast access.
		4. The State Store is also backed by the **checkpoint directory**, so the state can be recovered if the application fails.
		5. **Batch 2 arrives.**
		6. Spark retrieves the previous state from the State Store (from memory if available; otherwise it reconstructs it using the checkpointed state).
		7. Spark combines the previous state with the new records from Batch 2.
		8. Spark updates the state.
		9. The updated state replaces the previous state in the State Store.
		10. Spark writes the output to the sink according to the configured output mode:
			- **Complete** → writes the entire result table, like overwrite.
			- **Update** → writes only the rows whose values changed.
			- **Append** → writes only newly finalized rows (only supported for certain stateful operations, such as aggregations with event-time windows and watermarks).

	- ##### NOTE , In complete mode , the entire state is overwritten , meaning P , B, G, Y results will be written including " P " even if Batch 2 does not have any P values

- #### Update
	- In update only the new added values are written
	- ![](../attachments/Pasted%20image%2020260630202718.png)


## Sinks

- Sinks are the where the output resides

```python
# BASE 
import os
from pyspark.sql import SparkSession
from pyspark.sql.functions import from_json, col, window, count
from pyspark.sql.types import StructType, StructField, StringType, FloatType, LongType, TimestampType

# Kafka configuration
kafka_bootstrap_servers = os.getenv("KAFKA_BROKER")
kafka_topic = 'shopping_transaction '

# Kafka Consumer settings for Confluent Cloud
kafka_config = {
    'kafka.bootstrap.servers': kafka_bootstrap_servers,
    'subscribe': kafka_topic,
    'startingOffsets': 'earliest',  # To start from the earliest message
    'kafka.security.protocol': 'SASL_SSL',
    'kafka.sasl.mechanism': 'PLAIN',
    "failOnDataLoss" : "false",
    "kafka.ssl.endpoint.identification.algorithm" :  "https",
    'kafka.sasl.jaas.config': f'kafkashaded.org.apache.kafka.common.security.plain.PlainLoginModule required username="{os.getenv("KAFKA_API_KEY")}" password="{os.getenv("KAFKA_API_SECRET")}";',
    "startingOffsets" :"earliest"
}

schema = "timestamp DOUBLE, user_id INT, item STRING, quantity INT"

# Read data from Kafka
raw_stream = spark.readStream \
    .format("kafka") \
    .options(**kafka_config) \
    .load()

# Parse JSON data
parsed_stream = raw_stream.selectExpr("CAST(value AS STRING)") \
    .select(from_json(col("value"), schema).alias("data")) \
    .select("data.*")
     
# Transformation: Count the number of times each item is added
item_counts = parsed_stream.groupBy("item").count()
display(item_counts)
```

 1.  **Console Sink**  
	- Writes streaming query output to the console.  
	- Useful for debugging and testing.  
  
1. **File Sink**  
	- Writes output to files in formats like JSON, CSV, or Parquet.  
	- Suitable for batch-style processing and data archiving.  
  
1. **Kafka Sink**  
	- Writes processed data back to Kafka topics.  
	- Ideal for building real-time data pipelines.  
  ```python
  # Write the DataFrame to Kafka
checkpoint_location = "/mnt/checkpoint/kafka_sink"
query = kafka_df.writeStream \
    .format("kafka") \
    .option("kafka.bootstrap.servers", kafka_bootstrap_servers) \
    .option("topic", "shopping_items_aggregates") \
    .option("checkpointLocation", checkpoint_location) \
    .option("kafka.sasl.mechanism", "PLAIN") \
    .option("kafka.security.protocol", "SASL_SSL") \
    .option("kafka.sasl.jaas.config", f'kafkashaded.org.apache.kafka.common.security.plain.PlainLoginModule required username="{os.getenv("KAFKA_API_KEY")}" password="{os.getenv("KAFKA_API_SECRET")}";')\
    .outputMode("update") \
    .start()
  ```
  
 2. **Memory Sink**  
	- Writes the streaming output to an in-memory table.  
	- Useful for interactive analysis during development.  
  ```python
  checkpoint_location = "/mnt/checkpoint/memory_sink"
  item_counts_memory_query = item_counts.writeStream \
    .format("memory") \
    .queryName("streaming_table") \
    .option("checkpointLocation", checkpoint_location) \
    .outputMode("complete") \
    .start()
  ```
  
 3. **Delta Sink**  
	- Writes data to a Delta Lake table, enabling ACID transactions and efficient storage.  
	- Commonly used in production-grade streaming applications.  
```python
checkpoint_location = "/mnt/checkpoints/table_sinks"
parsed_query = parsed_stream.writeStream \
    .format("delta") \
    .outputMode("append") \
    .option("checkpointLocation", checkpoint_location) \
    .toTable("parsed_table")
```

 4. **Foreach Sink**  
	- Allows applying custom logic to handle each row in the stream.  
	- Executes custom code for every individual row.  
	- Suitable for sending records to external systems such as REST APIs or message queues.  

 5. **JDBC Sink**  
	- Writes output to relational databases using JDBC.  
	- Useful for integrating Spark with existing database systems.  
  
 6. **ForeachBatch Sink**  
	- Processes each micro-batch as a standard Spark DataFrame.  
	- Enables custom logic such as Delta `MERGE`, JDBC upserts, writing to multiple destinations, or invoking external services.  
	- One of the most common approaches for production streaming pipelines.


## `foreachBatch` Sink in Spark Structured Streaming  
  
- The `foreachBatch` sink allows you to execute custom logic on each micro-batch of a streaming query. Instead of Spark automatically writing the output to a sink, Spark passes each micro-batch as a standard Spark DataFrame to a user-defined function.  
  
- #### Use Cases  
  
	1. **Custom Data Sinks**  
		- Write data to destinations not natively supported by Structured Streaming, such as relational databases, REST APIs, or third-party systems.    
	2. **Batch-Level Transformations**  
		- Apply transformations or business logic on each micro-batch, such as deduplication, data validation, enrichment, or filtering.    
	3. **Upserts and Merge Operations**  
		- Perform `MERGE` operations to upsert data into Delta tables.  
		- Useful for Slowly Changing Dimensions (SCD), CDC processing, and maintaining the latest state of records.  
	4. **Multiple Output Destinations**  
		- Write the same micro-batch to multiple destinations, such as a Delta table, a database, and cloud storage.  
	5. **Custom Business Logic**  
		- Execute any Spark batch operation that is not directly supported by the built-in streaming sinks.  
  
- #### Common Production Use Cases  

	- Writing streaming data to **Delta tables** using `MERGE` (upsert).  
	- Writing data to **JDBC databases** (SQL Server, PostgreSQL, MySQL, etc.).  
	- Sending data to **REST APIs** or external services.  
	- Writing a single micro-batch to **multiple destinations**.  
	- Applying **batch-specific business logic** before persisting the data.  


- **`foreachBatch` Code for implementing multiple writes to multiple sinks**
```python
def myfunc(df, batch_id):  

	df = df.groupBy("color").agg(count("*").alias("count"))  
	# Destination 1  
	df.write.format("delta") \  
		.mode("append") \  
		.option("path", "/Volumes/workspace/stream/streaming/foreachsink/dest1") \  
		.save()  
	# Destination 2  
	df.write.format("delta") \  
		.mode("append") \  
		.option("path", "/Volumes/workspace/stream/streaming/foreachsink/dest2") \  
		.save()
	
df.writeStream \  
	.foreachBatch(myfunc) \  
	.option("checkpointLocation", "/Volumes/workspace/stream/streaming/foreachsink/checkpoint") \  
	.start()
```

- **`foreachBatch` Code for implementing UPSERT**
```python
from delta.tables import DeltaTable

stream_df = (  
	spark.readStream  
		.format("json")  
		.schema(schema)  
		.load("/mnt/input")  
)
item_counts = (  
	stream_df  
		.groupBy("item")  
		.agg(count("*").alias("count"))  
)

# Define the foreachBatch function
def merge_to_delta_table(batch_df, batch_id):  
  
	delta_table = DeltaTable.forName(spark, delta_table_name)  
	  
	(  
		delta_table.alias("target")  
		.merge(  
			batch_df.alias("source"),  
			"target.item = source.item"  
		)  
		.whenMatchedUpdate(  
			set={  
				"count": "source.count"  
			}  
		)  
		.whenNotMatchedInsert(  
			values={  
			"item": "source.item",  
			"count": "source.count"  
			}  
		)  
		.execute()  
	)

# Write stream with foreachBatch
checkpoint_location = "/mnt/checkpoints/foreach_sink"

query = (  
	item_counts.writeStream  
	.outputMode("update")  
	.foreachBatch(merge_to_delta_table)  
	.option("checkpointLocation", checkpoint_location)  
	.start()  
)  
  
query.awaitTermination()

```
  
> **Note:** Inside the `foreachBatch` function, each micro-batch is provided as a **regular Spark DataFrame**, allowing you to use all Spark batch APIs (`MERGE`, `UPDATE`, `DELETE`, `write`, `groupBy`, etc.).

