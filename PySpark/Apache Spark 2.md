
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
	- NOTE : If the foreign keys are string or anything , a hash function is performed which converts the key -> the output of hash function is a integer where MOD of partition can happen
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
	- ![](../attachments/Screenshot%20from%202026-04-27%2017-54-24.png)
	- ![](../attachments/Screenshot%20from%202026-04-27%2017-57-55.png)
	- ![](../attachments/Screenshot%20from%202026-04-27%2017-58-34.png)

#### NOTE : Types of Exchanges
	- Shuffle exchange - complete reshuffling of data between partitions
	- Broadcast exchange - The Spark driver collects a small table and sends a copy of it to every executor in the cluster.


- **Shuffle Hash Join**

	- After Shuffle , A Hash Table is created of the Smaller Table
	- The Data-frames in the Partition are joined by Mapping
	- Use Cases
		- Used when datasets are **medium-sized**.
	-  ![[Pasted image 20260311175431.png]]

- **Broadcast Join**

	- When 1 data set is very small and it completely fits in a Partition
	- The Driver Node then broadcast the Partition to other executor Nodes.
	- No Shuffling happens as we are just broadcasting an entire partition to other nodes.
	- **A table with Size less than 10 MB  can be broadcasted** - small table
	
	- ![[Pasted image 20260311195513.png]]
	- ![](../attachments/Screenshot%20from%202026-04-27%2018-06-47.png)
	- ![](../attachments/Screenshot%20from%202026-04-27%2018-07-27.png)

- ### Adjusting Size of Broadcast
	- `spark.conf.get("spark.sql.autoBroadcastJoinThreshold")`
		- This will show the current config
		- A couple of useful clarifications:
			- The value is in **bytes**
			- Default is usually **10 MB** (i.e., `10485760`)
			- If it returns `-1`, it means **broadcast joins are disabled**
	- `spark.conf.set("spark.sql.autoBroadcastJoinThreshold", <size in bytes> ) `

### Spark SQL Engine

- **Unresolved Logical Plan**
	- Your Query without confirming if the tables , columns are actually present or NOT.
- **Catalog has the Metadata**
	- Example: 
		- You have selected a column in Spark
		- But the columns does not exist
		- This happens because **Catalog does a small Analysis** and throws a **Analysis Exception**.
- **Resolved Logical Plan**
	- After Verifying that the columns , tables , etc are all valid. This is called Resolved Logical Plan
- **Optimized Logical Plan**
	- Spark Optimizes the code
- **Physical Plan**
	- Multiple Execution plans created based on the Optimized Logical Plan
	- **Cost Model** - Takes in all the Physical Plans and then Gives the least Expensive plan (computation , space , etc) - ie - Best Execution Plan


### Driver Memory Management

![](../attachments/Pasted%20image%2020260425024118.png)

#### Driver OOM ( Out Of Memory )

- **Drivers Heap memory Will broadcast the Broadcast Variables**

- #### Main Reasons for Driver OOM
	- **If Broadcast variable is more than the Heap Memory then OOM error**
	- CRITICAL commands such as **`df.collect()`** , executors send all data from **ALL EXECUTORS to the DRIVER**.

- If all executor sends data to driver then there is **OOM Error**
- ##### Example
	- Starting Spark
		- ![](../attachments/Screenshot%20from%202026-04-27%2017-09-28.png)
		- ![](../attachments/Screenshot%20from%202026-04-27%2017-10-28.png)
		- No Driver Memory in Spark UI
	- Setting Up a Driver Memory
		- Exit 
		- And Execute 
			- ![](../attachments/Screenshot%20from%202026-04-27%2017-12-22.png)
		- **UI** 
			- ![](../attachments/Screenshot%20from%202026-04-27%2017-13-11.png)
		- Hence we have allocated 1 GB of Memory
	- Now Lets create a very large Dataframe
		- ![](../attachments/Screenshot%20from%202026-04-27%2017-15-17.png)
		- **df.show()** by default only returns 20 records
		- **df.collect()** will return all the records to the driver ( very dangerous command)
		- ![](../attachments/Screenshot%20from%202026-04-27%2017-17-32.png)
		
	```
	26/04/27 17:16:18 ERROR Executor: Exception in task 10.0 in stage 1.0 (TID 11)
	java.lang.OutOfMemoryError: Java heap space
	```
	- #### Driver Memory Tags
		- `spark.driver.memory`
			- JVM process
		- `spark.driver.memoryOverhead`
			- NON JVM
			- container
### Executor Memory Management

![](../attachments/Pasted%20image%2020260425022142.png)

![](../attachments/Pasted%20image%2020260425030116.png)
## Total Memory = 11 GB

![](../attachments/Pasted%20image%2020260425022754.png)

![](../attachments/Pasted%20image%2020260425022745.png)

### Unified Memory

- Execution Memory used for Execution ( Temporary Storage ) and Storage Memory for ( Persistent Cache) Cache storage
- The separation between both Memory is Dynamic , so if Execution Memory is full it will take from Storage Memory
- **If Storage Memory is also full and there is NEED FOR EXECUTION MEMORY, MEMORY FROM STORAGE MEMORY IS EVICTED USING LRU ALGORIGHTM**
- LRU ( Least Used Data ( Cached Data) )
- Executor Memory has authority to Evict data from Storage Memory
- Storage Memory can Borrow Empty memory from Executor Memory.
### OFF HEAP MEMORY

- There will be **Garbage Collect (GC) cycles** happening in the **On-heap Memory**
- During the GC cycle, the program will me stopped , in this case **Off-Heap Memory can help**
- **Off-Heap Memory is managed by the Operating system.**
- Since GC are not managing the variables , it is the **programmers job to allocated and de - allocate memory**
- Off-Heap memory is slower than On-Heap memory


### Skewed Data

- Imagine you have a Product Category Column
- If one of the category in in 80% of the Data , then it is skewed.
### Executor OOM ( Out of Memory )

- When the Data is skewed and a single partition in a `groupby()` event has more size than a single executor
- #### Salting is used to Fix this issue

### Salting - Technique to eliminate the Skew-ness

- ![](../attachments/Pasted%20image%2020260427183915.png)
- If Data is Skewed
- we can create sub partitions of the skewed Data, and it can optimize memory by enabling Memory Disk slip


## Caching

- When we write a code 
```
df1 = read()
df2=df1.filter()
df3=df2.groupBy()
```
- Spark creates a DAG
- **And Spark will create df1 each time.**
- ![](../attachments/Pasted%20image%2020260427212446.png)
- Each time , **df1** is processed and created in memory and then the rest executes
- But using caching we can store **df1** in storage memory and it will utilize it from there
- ![](../attachments/Pasted%20image%2020260427212817.png)
- ![](../attachments/Screenshot%20from%202026-04-27%2021-37-50.png)
- ![](../attachments/Pasted%20image%2020260427213947.png)
	- ignore new_col as it was created before
	- But as per the PLAN we can see that `Yest AS Flag#10` , a new column is created again
- ##### Caching
	- ![](../attachments/Pasted%20image%2020260427214303.png)
	- **We are not creating a new Column in DAG**
	- ![](../attachments/Pasted%20image%2020260427214338.png)
- **ONLY CACHE if the Dataframe is small and we are using the small DF multiple times**
## Persist

- `df.cache() = df.persist(StorageLevel.MEMORY_AND_DISK)`
- ALL TYPE of caching is basically the persist command
- `cache()` is a special use case.
- `cache()` 
	- Tries to store in memory first
	- If memory is not enough , spill the rest to the disk

- **Storage LEVELS -**
	- **MEMORY_AND_DISK**
	- **MEMORY_ONLY**
		- Data only in RAM
		- If RAM is less memory , it will be recompute 
	- **DISK_ONLY**
	- **MEMORY_ONLY_2**
		- Data is replicated 2 times , for data tolerance
	- **OFF_HEAP**
		- We can use OFF_HEAP memory instead of RAM 
		- Note GC wont work , we need to maintain the memory
