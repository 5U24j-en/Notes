
Spark UI:  http://localhost:4040/jobs/

## Jobs, Stages, Tasks


![[Pasted image 20251026221609.png]]

- A Job can have Multiple Stages
-  A Stage can have Multiple Tasks

### Spark execution model

- A **Job** is triggered by an **action**
- A **Job** is divided into **Stages** based on **shuffle boundaries**
- A **Stage** contains multiple **Tasks**
- A **Task** is executed per **partition**

**A chain of narrow transformations can be executed in a single stage**, _until_ a wide transformation (shuffle) appears.

Wide Transformation will have another 1 stage

**Task is associated to the number of partitions**

If there are 200 partitioned, then there will be 200 tasks


![[Screenshot from 2025-12-17 16-44-31.png]]

- **Creating a DataFrame** is **not an action** and **does not trigger execution**.
- **Reading a DataFrame from existing files using `spark.read`** is **not an action**, but **when used with schema-less formats (like CSV/JSON), Spark may trigger internal jobs** to resolve metadata.
- **Using `.option("inferSchema", "true")`** is **not an action**, but it **forces Spark to run a job to scan the data and infer the schema**, which appears as a job in the Spark UI.
-  Hence there are 2 Jobs
- 1 Job will at-least have 1 stage + 1 Task 


### Example

![[Pasted image 20251217170849.png]]

1 ) Data Loading
2 ) Transformation
3 ) Action

- Once we run the ETL, there will be 3 Jobs running
- 2 Jobs while DataLoading ( .read due to Schemaless CSV file, inferSchema )
- 1 Job running due to .show() action command

![[Screenshot from 2025-12-17 17-11-53.png]]

NOTE: 1 job is skipped


**Step 1:**
![[Screenshot from 2026-01-19 18-47-37.png]]
![[Pasted image 20260119184858.png]]

**Step 2 :**
![[Screenshot from 2026-01-19 18-49-40.png]]
![[Pasted image 20260119185001.png]]

**Step 3:**
![[Screenshot from 2026-01-19 18-50-30.png]]

![[Pasted image 20260119185046.png]]

DAG: ( AQE NOT DISABLED , hence output is different)

![[Pasted image 20260119191932.png]]

![[Pasted image 20260119191948.png]]

![[Pasted image 20260119192409.png]]

![[Pasted image 20260119193031.png]]


-  Exchange Indicates that there is a shuffle .
-  A **shuffle** is the process of **redistributing data across partitions and executors** so that records with the same key end up together.
-  Shuffle is required when an operation needs to **see data from multiple partitions together**.
	- Example:
		-  `groupBy(key)`
		-  `join`
		-  `distinct`
		- `orderBy`
		-  `reduceByKey`
		
- These operations **cannot be done independently per partition**, so Spark must:
	1. Re partition the data by key
	2. Move data across the network
	3. Regroup it in new partitions


#### NOTE : Default Max Size of a Partition is 128 MB


## Joins

**Join is a Wide Transformation**

- Assume we have 2 Datasets with approx 450 MB of Data and we want to join the Data.
- **Each Core can perform 1 Task.**
- ![[Pasted image 20260308230329.png]]

- When you perform a **wide transformation** (like a `join`, `groupBy`, or `orderBy`), Spark usually has to move data around the cluster so that related records end up together. This massive reorganization of data is called a **shuffle**.
- Whenever a shuffle happens with DataFrames, Spark looks at a specific configuration called `spark.sql.shuffle.partitions` to decide how many partitions to output. By default, the developers of Spark set this value to exactly **200**.
- Hence for Join to happen the Data need to be **Shuffled**.
- Example
		`df1.join(df2, "customer_id")`
- Hash function is applied to the join key -> hash(customer_id)
- For every single row of data, the HashPartitioner runs this basic formula:
	`Target_Partition_ID = Hash(Key) % Number_of_Shuffle_Partitions`
- ![[Pasted image 20260311173543.png]]
- All JOINS follow the Same process Till **Shuffled State** 

- ### Join Strategies

	-  **Broadcast Hash Join**
	-  **Shuffle Hash Join**
	-  **Sort Merge Join**
	-  **Broadcast Nested Loop Join**


- **Sort Merge Join**

	-  After the Shuffle , Spark sorts them by join key and then Merges them
	-  Default Join
	-  Use Cases
		- Used for **large datasets**.
		- Works best when data is **already sorted or partitioned**.

-  **Shuffle Hash Join**

	- After Shuffle , A Hash Table is created of the Smaller Table
	- The Data-frames in the Partition are joined by Mapping
	- Use Cases
		- Used when datasets are **medium-sized**.
	-  ![[Pasted image 20260311175431.png]]

- **Broadcast Join**

	- When 1 data set is very small and it completely fits in a Partition
	- The Driver Node then broadcast the Partition to other executor Nodes.
	- No Shuffling happens as we are just broadcasting an entire partition to other nodes.
	- ![[Pasted image 20260311195513.png]]
	- 