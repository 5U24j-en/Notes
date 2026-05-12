

## Delta Lake

- **Traditional Data Lake ( like S3 ) problems**
	- Just using parquet , csv on cloud storage
	- NO ACID transaction ( data can be corrupt if 2 jobs are written at once)
	- Hard to handle schema changes
	- No east way to track changes or roll back to earlier versions

- **In Delta Lake**
	- ACID Transactions
	- Schema Enforcement and evolution
	- Time travel
	- Unified Batch + Streaming
	- Data lineage and audit

- ![](../attachments/Pasted%20image%2020260506214540.png)
- A Delta Table + Data Lake = Delta Lake / Lakehouse

## Creating a Delta Table

- **Spark**
```python
from delta.tables import DeltaTable

DeltaTable.createIfNotExists(spark) \
	.tableName("deltalake.default.fistdeltaapi") \
	.addColumn("id","INT") \
	.addColumn("salary", "INT") \
	.execute()
```
- **SQL**
```sql
CREATE TABLE deltalake.default.first_table
(
	id INT NOT NULL,
	salary INT
) USING DELTA;
```
- **Spark - IdentityGenerator() AND `generatedAlwaysAs`**
```python
from delta.tables import DeltaTable , IdentityGenerator
from pyspark.sql.types import *
from pyspark.sql.functions import *

DeltaTable.createIfNotExists(spark) \

	.tableName("deltalake.default.generated_table") \
	.addColumn("surrogate_key",dataType=LongType(),generatedAlwaysAs=IdentityGenerator()) \
	.addColumn("name",dataType=StringType()) \
	.addColumn("salary", dataType=LongType()) \
	.addColumn("salaryaftertax", dataType=DoubleType(), generatedAlwaysAs="CAST( salary * 0.7 AS DOUBLE)") \
	.execute()
```
- **Inserting Values to table**
```sql
INSERT INTO deltalake.default.generated_table(name, salary)
VALUES
	('aa', 100),
	('bb', 200);
```
- **Output**
	- ![](../attachments/Pasted%20image%2020260508114140.png)
	- **Column values are automatically created based on the logic**

- **Tables Created**
	- ![](../attachments/Pasted%20image%2020260508113816.png)
- 

## Delta Lake 

-  **Files are stored in partitions/folders or just in a folder in a Data Lake**
- **Adding a Layer to these files that leads to enforcing ACID properties on these files is called Delta Lake**
- #### Created  DataFrame in Memory to write into files
```python
data = [(1,100,'aa'),(2,200,'bb'),(3,300,'cc'),(4,400,'dd'),(5,500,'ee')]

df = spark.createDataFrame(data, ['id', 'salary', 'name']) 
display(df)
```
- ### Writing to Delta Lake
	- ![](../attachments/Pasted%20image%2020260508110235.png)
	- After Each write a **`JSON`** and **`crc`** file is created 
	- Each Delta Log JSON file has an Action Column , in this case we have column **`add` -> `json`** 
	- If we delete any data , the action column will re `remove`
		- ![](../attachments/Pasted%20image%2020260508110659.png)

	- If the parquet file was corrupted due to any reason, the `parquet` file will be there , but **NO JSON file** will be created. Hence even though file exist there is no transaction
		- Hence Atomicity is maintained


- ### Schema Enforcement

	- ![](../attachments/Pasted%20image%2020260508111729.png)
	- ![](../attachments/Pasted%20image%2020260508112111.png)

- ### Schema Evolution

	- Evolving the Schema - when source job is expected to change number of columns.
```python
df.write.format("delta") \
	.mode("append") \
	.option("mergeSchema", "true") \
	.save("/Volumes/deltalake/default/deltavol/demosink/")
```

- `option("mergeSchema", "true")` -> **Schema evolution** 
	- ![](../attachments/Pasted%20image%2020260508112504.png)
	- ![](../attachments/Pasted%20image%2020260508112601.png)


### Reading Delta Data 

- To read the Files like a Table
- ![](../attachments/Pasted%20image%2020260508115616.png)
```sql
SELECT * FROM delta.`/Volumes/deltalake/default/deltavol/demosink/`
```

**NOTE :  We are USING BACK-TICK  NOT ''.**

- **OUTPUT**
	
	- ![](../attachments/Pasted%20image%2020260508120028.png)
	- We Wrote it 3 times
	- 3 Files created - with _delta_lake folder
 		- ![](../attachments/Pasted%20image%2020260508120103.png)

### Overwrite - Delta Lake

- **Dataframe**
```python
data = [(0,900,'overwrite',-1),(8,200,'overwrite',-1),(7,400,'overwrite',-1),(3,900,'overwrite',-1),(21,1200,'overwrite',-1)]
df = spark.createDataFrame(data, ['id', 'salary', 'name','suscolumn'])
df.display()
```
-  **Overwriting**
```python
df.write.format("delta") \
	.mode("overwrite") \
	.save("/Volumes/deltalake/default/deltavol/demosink")
```

- **Reading Table**
```sql
SELECT * FROM delta.`/Volumes/deltalake/default/deltavol/demosink/`
```
- **OUTPUT**
	- ![](../attachments/Pasted%20image%2020260508122022.png)

- **Files**
	- ![](../attachments/Pasted%20image%2020260508122108.png)
	- Delta Lake uses **transaction logs (`_delta_log`) + immutable parquet files**. `mode("overwrite")` does **not immediately physically delete** the old parquet data files.
	- Instead:
		1. New parquet files are written
		2. Delta transaction log marks old files as **removed**
		3. Future reads only see the new files
	- But physically on storage:
		- old parquet files may still exist temporarily
		- they are considered **obsolete / unreferenced**
		- Delta keeps them for **time travel / rollback**
	- **Files will never be deleted until VACUUM is run**
	- **To permanently remove old files**
**sql**
```sql
VACUUM delta.`/Volumes/deltalake/default/deltavol/demosink`
```
OR 
**python**
```python
from delta.tables import DeltaTable  
  
deltaTable = DeltaTable.forPath(spark, "/Volumes/deltalake/default/deltavol/demosink")  
deltaTable.vacuum()
```
- Once Vacuum is run the age of the files will be checked if the age is greater than the default the files will be deleted and by **Default retention is usually 7 days (168 hours) for safety.**


### Schema Overwrite

- ###### Overwriting Files that have new columns

```python
data = [(21,200,'schemaoverwrite',2),(2,400,'schemaoverwrite',2),(3,600,'schemaoverwrite',2),(4,800,'schemaoverwrite',2),(5,900,'schemaoverwrite',2)]
df = spark.createDataFrame(data, ['id', 'salary', 'name','tip'])
display(df)
```
- New Data
	- Changed Column name to `tip`
	- ![](../attachments/Pasted%20image%2020260508141855.png)

- Normal Overwrite wont work
- OverwriteSchema
	- `option("overwriteSchema", True)`
```python
df.write.format("delta") \
		.mode("overwrite") \
		.option("overwriteSchema", True) \
		.save("/Volumes/deltalake/default/deltavol/demosink")
```

- ![](../attachments/Pasted%20image%2020260508142257.png)

- ### DML

- Create DF and write first part . (first job)
```python
data = [(1,100,'aa',1),(2,200,'bb',1),(3,300,'cc',1),(4,400,'ee',1)]
df = spark.createDataFrame(data, ['cust_id', 'income', 'name', 'tip'])
df.write.format("delta") \
		.mode("append") \
		.save("/Volumes/deltalake/default/deltavol/dmlsink/")
```
- Create DF2 and write the second part. (second job )
```python
data = [(21,340,'ab',1),(12,290,'at',1),(13,560,'ah',1),(41,410,'ag',1)]
df = spark.createDataFrame(data, ['cust_id', 'income', 'name', 'tip'])
df.write.format("delta") \
		.mode("append") \
		.save("/Volumes/deltalake/default/deltavol/dmlsink/")
```
- Update a Record (third job)
```sql
UPDATE delta.`/Volumes/deltalake/default/deltavol/dmlsink/`
SET income = 1000 WHERE cust_id=4
```

The Flow ->
![](../attachments/Pasted%20image%2020260508150537.png)


### Deletion Vectors

- Storage Cost Optimized
- When we update a partition , instead of create a file with the entire partition , We are creating a small partition file with just 1 record.
- Deletion vector helps in updating the delta log and efficiently managing the files

### UPSERT ( MERGE )

- 




