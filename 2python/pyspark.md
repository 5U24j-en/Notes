
```python
#Workers with Highest Salary
import pyspark
import pyspark.sql.functions as F
from pyspark.sql.window import Window

w = worker.alias("w")
t = title.alias("t")

result = w.join(t, on=(w.worker_id == t.worker_ref_id), how="left").filter(F.col("worker_title").isNotNull())
window_spec = Window.orderBy(F.col("salary").desc())
result = result.withColumn("salary_rank", F.dense_rank().over(window_spec))
result = result.filter(F.col("salary_rank")==1).select("worker_title")
```

```python
#Posts which reacted to heart
import pyspark
import pyspark.sql.functions as F

result = facebook_posts.join(facebook_reactions, on = "post_id", how="left")
result = result.drop(facebook_reactions.poster)
result = result.filter(F.col("reaction")=="heart")
result = result.select("post_id", "poster", "post_text", "post_keywords", "post_date").distinct()
result.toPandas()
```

```python
#Latest Updated Records
import pyspark
from pyspark.sql.window import Window
import pyspark.sql.functions as F

window_spec = Window.partitionBy('id').orderBy(F.desc('salary'))

result = ms_employee_salary.withColumn('rn', F.row_number().over(window_spec))
result = result.filter(F.col('rn')==1)
result = result.drop(F.col('rn'))
result.toPandas()
```

```python
#Total Cost of Orders
import pyspark
import pyspark.sql.functions as F

c = customers.alias("c")
o = orders.alias("o")
c = c.withColumnRenamed("id", "cust_id")
o = o.withColumnRenamed("id", "order_id")

result = c.join(o, on = (c.cust_id==o.cust_id), how="left")
result = result.drop(o.cust_id)
result = result.groupBy("cust_id","first_name").agg(F.sum("total_order_cost").alias("total_order_cost"))
result = result.filter(F.col("total_order_cost").isNotNull())
result.toPandas()
```

```python
# Average Department Salary
import pyspark
import pyspark.sql.functions as F
from pyspark.sql.window import Window

window_spec = Window.partitionBy("department")

result = employee.withColumn("dept_avg_salary", F.avg("salary").over(window_spec))
result = result.select("department","first_name","salary","dept_avg_salary")
result.toPandas()
```


FUNCTION NOTE:


- **`from_json(column, schema)`**
	- **column** → the JSON string column
	- **schema** → tells Spark how the JSON is structured

- DataFrame Creation

	- **In Memory**
		- `df = spark.createDataFrame(data, schema)`
			- Schema , can be defined with a schema variable OR without schema , OR directly defining the columns
			- `df = spark.createDataFrame(data, ["id", "name", "salary"])`
	
	- **Reading from disk**
		- `df = spark.read.format("<format>").option("key", "value").load("<path>")`
			- **CSV -** `df = spark.read.csv("employees.csv")`
				- With Header - `df = spark.read.csv("employees.csv",  header=True)`
				- Inferring Schema ( 2 jobs ) - `df = spark.read.csv("employees.csv", header=True, inferSchema=True)`
		- **Reading with Schema**
			- `df = spark.read.schema(schema).json("weather.json")`
				- **schema** is Defined
		- **Reading Data and Loading and Format** 
			- `df = spark.read.format("parquet").load("data/")`
			- `df = spark.read.format("delta").load("/mnt/delta/weather")`

	- **Reading from Cloud (ADLS) :**
		- `df = spark.read.format("delta").load("abfss://container@storageaccount.dfs.core.windows.net/weather")`

	- **Reading from Database using JDBC  ( Java Database Connectivity. )**
```python
	df = spark.read \
		    .format("jdbc") \
		    .option("url", "jdbc:mysql://localhost:3306/company") \
		    .option("dbtable", "employees") \
		    .option("user", "root") \
		    .option("password", "password") \
		    .load()
```
- 
	- **Reading Using a SQL query in a DB**
```python
df = spark.read \  
		.format("jdbc") \  
		.option("url", jdbc_url) \  
		.option(  
		"dbtable",  
		"(SELECT * FROM employees WHERE salary > 50000) temp"  
		) \  
		.option("user", user) \  
		.option("password", password) \  
		.load()
```

