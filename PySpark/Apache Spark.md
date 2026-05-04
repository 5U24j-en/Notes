Source: https://www.youtube.com/watch?v=FNJze2Ea780&t=4922s

Spark UI -  http://localhost:4040/jobs/

- **Cluster** : Collection of Devices/Computer/Server connect using a network.
- **Node :** A single computer/server/compute


<h2>Spark Architecture </h2> 

![[Pasted image 20251026214857.png]]

-  **Cluster Manager** : Manages the cluster
	-  The Cluster Manager first creates a Driver Node
 - **Driver Node :** 
	 - Once the program is run, it first goes to the Driver Node and then the Driver Node breaks down the code into stages like transformation, stages, jobs, etc and provides these details to the Cluster Manager.
	 - The Driver then requests the cluster manager to create Worker Nodes as per requirement.
- **Worker Node :** 
	- These are individual compute resources that perform the divided tasks requested by the Driver Node. (Master - Slave architecture )


![[Pasted image 20251026215010.png]]


<h2>Lazy Evaluation</h2>
A programming strategy where the code is only executed/ computed only when the Action trigger is executed.

-  Example, If there is a data set where there are multiple transformations on the dataset like filter, adding column, etc, PySpark ( Master Node ) will only create a plan and will execute the transformation in the most efficient way once the trigger action is called.
- So transformation 1 , transformation 2 will be in 1->2 order but PySpark may change the order 2->1 since it is the most efficient way to execute the code.


**Jobs :** The entire code.
**Stage :** Jobs are divided to stages
**Task :** Jobs are divided into sub tasks

![[Pasted image 20251026221609.png]]

<h4>Apache Spark is the main Engine and it has multiple low level APIs like Python , Scala, SQL, R</h4>
<h4>Apache Spark is written in Scala</h4>

**Schema**: The Data frame column datatype

![[Pasted image 20251028225711.png]]

You can load a Data with your own schema of a Data-frame by storing a Schema in a string variable and calling it while loading.

![[Pasted image 20251028230632.png]]

Apache Spark : 
- Massive Parallel processing using Distributed machines.
- A group of machines is called a cluster.
- A single machine is a Node.

2 approaches to handle big data.
- Monolithic
	- Upgrading RAM, adding CPU cores, 
	-  This is known as **Vertical Scaling**
	- Low Availability as there is only 1 machine
- Distributed
	- Add multiple Systems/ Machines/ Nodes
	-  **Horizontal Scaling**
	-  High Availability as if 1 machine goes down there are other machines

Apache Spark VS Hadoop MapReduce
- Hadoop MapReduce is also a distributed computing engine.
-  MapReduce used to write all the result to the Disks.
-  It is time taking.
-  Apache Spark uses RAM and Disk
-  Spark is 100X times faster than MapReduce.

![[Pasted image 20251128154839.png]]


Apache Spark allows you to perform batch processing and stream processing at the same time.

Apache Spark has a Master-Slave architecture. One Master Node and multiple working Nodes.

Terms:
- Resource Manager:
	- Allocates Resources
	- Types of Resources
		- Driver
		- Workers
- Driver + Worker = Cluster of Nodes

Driver Node orchestrates the work done by the Working Nodes 

Master Slave Architecture changes with the state of the application.
 - Example:
	 - When the application is in resource allocation state, The resource manager is the Master has the instructions and share it to the Driver Node.
	 - When the application is in the data processing stage the **Driver Node is the Master**

State 1
![[Pasted image 20251128160508.png]]

State 2
![[Pasted image 20251128160540.png]]

**Main Architecture**
![[Pasted image 20251128160703.png]]

- PySpark/Java/Scala code is submitted to the cluster Manager.
- The Cluster Manager first creates a Driver Program which has the instruction of the code.
- The Driver Program then instructs the cluster manager to create the required nodes as per the code. ( 2 way arrow represent it)
- Cluster Manager creates the Worker Node as instructed by the Driver Program
-  The Worker Node is just the Machine. The Executor are the actual program that executes the Code.
- The Worker Nodes have a direct 2 way communication with the Driver Program. 
- The Task are provided to the Executors and the executors handle the actual work.
- In the Entire Flow the cluster manager only handles the resource management like a company Manager. The actual work Orchestration is handled by the Driver Program like a Team Lead.

Documentation Reference Link: https://spark.apache.org/docs/latest/cluster-overview.html

**Cluster Manager**

- Cluster Manager / Resource Manager can be a separate application or sparks has its own cluster Manager.
- We can also use Kubernetes and other apps like Mesos, YARN, etc

**SparkContext** is now **SparkSession**

- Spark applications runs as independent set of processes on a cluster and is coordinated by the **SparkContext** Object in your main Program.

**Driver Node**

- Cluster Manager creates/installs a Application Master Container.
- Application Master Container is responsible for all the orchestration, driver program activities, etc.
- Application Master Container Has
	- PySpark Main ( PySpark Driver )
		-  An Optional Driver that is only required if we are Using Pyspark
		-  PySpark main is used to understand the Python code
		- PySpark main will convert the Python code into JVM main (Java code) using a process called as **Py4J**.
		- Apache Spark is written in Scala so it need JVM to execute.
	- JVM Main ( Application Driver )

DRIVER NODE
![[Pasted image 20251128164606.png]]


**Worker Node:**

- Each Worker Node needs to install JVM to execute the code.
- In Special case if we create a Python UDF ( not recommended ) Python Interpreter is also installed
- Hence it is not recommended to create Python UDF as we need to install Python driver in each node and it increases the workload process

WORKER NODE
![[Pasted image 20251128164829.png]]

**New Info:** Spark is planing to replace JVM to C++ based executor as it is fast and can be processed native/use less resources.


**SparkSession**

- Spark Session has 3 context
	-  SparkContext
	- HiveContext
	- SQLContext
	
- These "contexts" are entry points to Apache Spark's functionality.

**Lazy Evaluation and Action**

- Whatever transformation you write in code, Spark will create a plan which has the transformations
- It will only execute the plan if you execute an action command.
- Action commands are - .show(), .count(), .collect() , etc.
- This entire process is a Spark Job.

**Lazy Evaluation Demo**

Code :

Step 1:
![[Pasted image 20251216193945.png]]

Step 2:
![[Screenshot from 2025-12-16 19-40-36.png]]
- No Jobs, execution done, only DAG ( execution plan is created )


Step 3:
![[Pasted image 20251216194904.png]]
- Spark Job is run due to Action Command


### To View the Plan

df_new.explain()

![[Pasted image 20251216195108.png]]
- Read from the Bottom.
- First it scanned all the Data
- The second line show that Spark optimized the code.
- Due to Lazy Evaluation, it combined both the transformations, and optimized the code.
- Project --> to display the Output

### To view the DAG

-  Expand the Spark Jobs which was executed after action command 
- ![[Pasted image 20251216195446.png]]
- Click on view
- ![[Pasted image 20251216195527.png]]
-  DAG --> Directed Acyclic Graph

NOTE:  While running code in jupyter notebook, the default way to view Spark UI is http://localhost:4040/jobs/

Sample Job run result

DAG
![[Pasted image 20251216201422.png]]

Notebook Path: /home/userx/Desktop/PySpark/SparkJobDAC.ipynb


### DAG ( Directed Acyclic Graph )

- Spark creates a DAG for every Job
- The flow has to be One way, straight line


### **Partitions**

The below is the Data and the 2 Nodes

![[Pasted image 20251216201924.png]]

The Data/ Records are split into Partitions and each partition is given to a worker Node.
This is parallel processing 

![[Screenshot from 2025-12-16 20-22-30.png]]

### RDD  ( Resilient Distributed Dataset)

 - RDD is a type of Data structure in Apache Spark
 - It is like a LIST of Logical Partitions
 - This is distributed to the executor/ worker Node.
 - RDDs are immutable / cannot change
 - Example
	 - We have 3 transformations.
	 - After first tansformation it will create RDD1, then after second transformation it will create RDD2, etc
	 - ![[Pasted image 20251216203617.png]]
	 - So when we do a 
			df=df.select("xyc") ( RDD 1)and
			df=df.filter(col("xyz")=y) ( RDD 2 )

	- It DOES NOT create a dataframe every time, It creates new RDD after each transformation
	- Hence when RDD 2 fails , it has DAC for the plan and RDD 1 as previous data

### **Transformations**

Types of Transformations
- Narrow Transformations
	- Example: filter, Select
-  Wide Transformations
	- Example: GroupBy

Narrow Transformation:
- The function on each partition is **not** dependent on the other partitions
- Like, you filtering data can be done to each partition independently

Wide Transformation:
- Function where data/records from each partition should be considered
- Like when we use groupBy we need to consider data from each partition to create the groups
- If there is a column city, then NewYork value will be present in multiple partitions, hence the function is dependent of all partitions

![[Screenshot from 2025-12-16 23-08-40.png]]

- During a **wide transformation**, Spark performs a **shuffle** to reorganize data across partitions.  
- For example, in a `groupBy(City)`, all records belonging to the same `City` are sent to the **same shuffle partition** so that aggregation can be performed correctly.  
- A single partition may contain **multiple cities**, but **all records for any given city will exist in exactly one partition**.

![[Pasted image 20251216234305.png]]

![[Pasted image 20251216234442.png]]

DAG

![[Pasted image 20251216234536.png]]                                                            
![[Pasted image 20251216234614.png]]

### Repartition VS Coalesce

Default Partition Size = 128 MB
Default Block Size = 128 MB

- Repartition is used to create more partitions of the Data.
- Coalesce is used to merge paritions or reduce partitions

### `repartition()` → **Always causes a shuffle**

> When `repartition()` is used, Spark performs a **full shuffle**, redistributing data evenly across the specified number of partitions.

- Uses hash partitioning
- Data moves across executors
- Shuffle partitions are created
![[Pasted image 20251217001627.png|507]]
### `coalesce()` → **Usually NO shuffle** 

> `coalesce()` **reduces the number of partitions without a full shuffle** by merging existing partitions.

Key points:

- Data is **not redistributed**
- Spark just **collapses partitions**
- Tasks read from multiple parent partitions
- Reshuffling happens only when explicitly requested
- Coalescing to a **higher** number of partitions (rare)
- AQE decides to rebalance (Spark 3+)
- ![[Pasted image 20251217002027.png]]


![[Pasted image 20251217001721.png]]

# EXECUTION PLAN
## .explain()

Reference: https://dbc-4ec3cb51-0ee8.cloud.databricks.com/editor/notebooks/1423163780420517?o=3542347254064403

Video: https://youtu.be/761SQ9Hxbic?si=3bvA1J9E4ENfwz_t

- Table Movies Used

![[Pasted image 20260114161846.png]]

- We run a Query and then use .explain() function to understand the execution plan.


![[Pasted image 20260114161704.png]]

- .explain("extended") -> Output

![[Pasted image 20260114161030.png]]

```
== Parsed Logical Plan == 
'Filter '`>`('release_year, 2010) 
	+- 'Project ['title, 'studio, 'imdb_rating] 
		+- 'UnresolvedRelation [workspace, default, movies], [], false 
		
		
== Analyzed Logical Plan ==
title: string, studio: string, imdb_rating: string 
Project [title#18335, studio#18339, imdb_rating#18338] 
+- Filter (release_year#18337L > cast(2010 as bigint)) 
	+- Project [title#18335, studio#18339, imdb_rating#18338, release_year#18337L] 
		+- SubqueryAlias workspace.default.movies 
			+- Relation workspace.default.movies[title#18335,industry#18336,release_year#18337L,imdb_rating#18338,studio#18339,budget#18340,revenue#18341,unit#18342,currency#18343,language#18344] parquet 
			
== Optimized Logical Plan == 
Project [title#18335, studio#18339, imdb_rating#18338] 
	+- Filter (isnotnull(release_year#18337L) AND (release_year#18337L > 2010)) 
		+- Relation workspace.default.movies[title#18335,industry#18336,release_year#18337L,imdb_rating#18338,studio#18339,budget#18340,revenue#18341,unit#18342,currency#18343,language#18344] parquet 
		
== Physical Plan == 
*(1) ColumnarToRow 
+- PhotonResultStage 
	+- PhotonProject [title#18335, studio#18339, imdb_rating#18338] 
		+- PhotonScan parquet workspace.default.movies[title#18335,release_year#18337L,imdb_rating#18338,studio#18339] DataFilters: [isnotnull(release_year#18337L), (release_year#18337L > 2010)], DictionaryFilters: [(release_year#18337L > 2010)], Format: parquet, Location: PreparedDeltaFileIndex(1 paths)[s3://dbstorage-prod-ssw1w/uc/8a3515b8-bf97-4d4c-b84a-921b9af0942e..., OptionalDataFilters: [], PartitionFilters: [], ReadSchema: struct<title:string,release_year:bigint,imdb_rating:string,studio:string>, RequiredDataFilters: [isnotnull(release_year#18337L), (release_year#18337L > 2010)] 
		
== Photon Explanation == 
The query is fully supported by Photon. 
== Optimizer Statistics (table names per statistics state) == 
	missing = 
	partial   = 
	full          = movies

```


# Execution

- .explain() : Without arguments only prints a **Physical Plan**.
- .explain("extended") : Prints the **Logical** and **Physical Plan**.
- Once Query is Run below steps are done.

![[Pasted image 20260114163017.png]]

- ## **Unresolved Logical Plan:** (== Parsed Logical Plan == )

	- This plan is created **right after Spark parses your SQL / DataFrame code**
	- Spark understands the _syntax_
	- But it **has not yet checked**:
		- Whether the table exists
		- Whether the columns exist
		- Column data types
		- Ambiguous references
	- Only Syntax of the Code is Validated  
	- At this stage, Spark only knows _what you asked_, not _whether it is valid_.

	
	- == Parsed Logical Plan == 
		'Filter '`>`('release_year, 2010) 
			+- 'Project ['title, 'studio, 'imdb_rating] 
				+- 'UnresolvedRelation [workspace, default, movies], [], false 

**NOTE:**  Always read the PLAN from the **bottom** ( The first Execution )  to **TOP**

	- 'UnresolvedRelation - Means the Table is Unknown ( since its only syntax )
	- 'Filter - Filter the year
	- 'Project - means select title, studio, etc.

**NOTE:**  Everything has `'` before : The `'` prefix indicates **unresolved attributes**.



- ## Catalog:

	-  Catalog has all the actual details about the Data about the **REFERENCES** like Tables, columns names, etc , .
	-  Once the **Unresolved Logical Plan** is created. The **Unresolved Logical Pla**n reference to the **CATALOG** to get the actual details and confirm if the tables , references are actually available or NOT.
	- One the **Unresolved Logical Plan** gets and confirms the references, it becomes **Resolved Logical Plan**.

- ## Resolved Logical Plan:

	-  The Logical plan after having the actual referenced Data.



	-  == Analyzed Logical Plan ==
		title: string, studio: string, imdb_rating: string 
		Project [title#18335, studio#18339, imdb_rating#18338] 
		+- Filter (release_year#18337L > cast(2010 as bigint)) 
		   +- Project [title#18335, studio#18339, imdb_rating#18338, release_year#18337L] 
		      +- SubqueryAlias workspace.default.movies 
		         +- Relation workspace.default.movies[
		              title#18335,
		              industry#18336,
		              release_year#18337L,
		              imdb_rating#18338,
		              studio#18339,
		              budget#18340,
		              revenue#18341,
		              unit#18342,
		              currency#18343,
		              language#18344
		            ] parquet


	- First Step ->  Relation workspace -> This loads the Data
		- Spark **found the table**
			- Knows:
				- Exact schema
				- Column data types
				-  Storage format (`parquet`)
		-  Each column now has a **unique expression ID** (`#18335`, etc.)
	    - Example: `release_year#18337L` → `L` means **Long (bigint)**
		- This confirms the table exists and schema is valid.

	-  Second Step -> SubqueryAlias workspace.default.movies
	- Spark assigns a **logical alias** to the table
		- Required for:
			- Nested queries
			- Qualified column references
			- SQL correctness

	- Third Step -> Project [title#, studio#, imdb_rating#, release_year#]
		-  Project similar to  SELECT
		- release_year# added temporarily as we need it for **filter step**.
	
	- Fourth Step ->  Filter (release_year#18337L > cast(2010 as bigint)) 
		- Filter Logic Applied

	-  Fifth Step -> Project [title#18335, studio#18339, imdb_rating#18338] 
		- SELECTing the OUTPUT.

	- title: string, studio: string, imdb_rating: string
		-  OUTPUT Schema

- ## Optimized Logical Plan 

	- The transition from a **Resolved (Analyzed) Logical Plan** to an **Optimized Logical Plan** is done by **Spark SQL’s Catalyst Optimizer**, using a **rule-based optimization framework**.


	- == Optimized Logical Plan ==
		Project [title#18335, studio#18339, imdb_rating#18338] 
		+- Filter (isnotnull(release_year#18337L) AND (release_year#18337L > 2010)) 
		   +- Relation workspace.default.movies[
		        title#18335,
		        industry#18336,
		        release_year#18337L,
		        imdb_rating#18338,
		        studio#18339,
		        budget#18340,
		        revenue#18341,
		        unit#18342,
		        currency#18343,
		        language#18344
		      ] parquet


	-  Filter (isnotnull(release_year#18337L) AND (release_year#18337L > 2010))
		- Above Code is optimized as we are first filtering and then reading the Data.


- ## Physical Plan

	-  == Physical Plan ==
		*(1) ColumnarToRow
		+- PhotonResultStage
		   +- PhotonProject [title#, studio#, imdb_rating#]
		      +- PhotonScan parquet workspace.default.movies[
		           title#, release_year#, imdb_rating#, studio#
		         ]
		         DataFilters: [isnotnull(release_year#), (release_year# > 2010)]
		         DictionaryFilters: [(release_year# > 2010)]
		         Format: parquet
		         Location: PreparedDeltaFileIndex(...)
		         ReadSchema: struct<title:string,release_year:bigint,imdb_rating:string,studio:string>
		         RequiredDataFilters: [isnotnull(...), (release_year# > 2010)]


	-  ***(1) ColumnarToRow** 
		- Data is stored in Parquet Format which is a column based written Data, but to display we need to show in Row based written data, so sparks convert it into Row based
	- At this stage Spark has decided:
		- Exact operators
		- Execution engine
		- Filter pushdown
		- Columnar vs row execution
		- File scan strategy
	-  Multiple Physical Plans are created and the **AQE** selects the Most efficient Plan.
	
	
## -  Photon Query Engine

	- Photon Query Engine used to execute Query Very Fast.
	- Written in C++



We can use .explain("formatted") for cleaner view.




[[Apache Spark 2]]

