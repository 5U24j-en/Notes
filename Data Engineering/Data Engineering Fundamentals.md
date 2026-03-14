
- Data Transformation
- Data Serving
- DBA - Database Administrator

- UpStream - DownStream

	- UpStream - Data Source
	- DownStream - Data Consumer

	- Requirements are usually given by DownStream

- OLTP Database:
	- OLTP - Online  Transactional Processing
	- Managed by DBA / SWE - raw data from data source is written it in the Database in small chunks in a software
	- Database efficient for Writes and Updates.
	- Eg. Relational Databases, MySQL, Oracle, Database
	- A Data Engineer will treat a OLTP Database are the source.
	- DBA manages Database by Modeling.
	- Modeling --> Normalization ( 1 NF, 2 NF, 3 NF )

- OLAP Database:
	- OLAP - Online Analytical Processing
	- OLAP = Data Warehouse


![[Pasted image 20260102172033.png]]


- For OLAP Database , Data-modelling is different.
-  We use **Dimensional Modelling** for OLAP Databases

![[Pasted image 20260102172351.png]]


## Data Warehouse and Layers


-  Staging Layer
-  Core Layer

![[Pasted image 20260102174247.png]]


## Incremental Loading

![[Pasted image 20260102175445.png]]


## Dimensional Modeling

Modeling Technique where we store Data in the form of Facts and Dimensions

There are 2 Types of Schema:
- Star Schema
- Snowflake Schema


### Star Schema

![[Pasted image 20260106165708.png]]

Selecting Dimensions:

Example: 
- All Customer Data will be a Single Dimension. Example: Customer Name, Customer Address , etc
- All Product Details will a another Dimension. Example: Product Name, Product Category, etc
![[Pasted image 20260106170240.png]]



### Snowflake Schema

- This is rarely Used
- There is a Hierarchy of Dimensions

![[Pasted image 20260106170616.png]]




# Slowly Changing Dimensions ( SCD )

Ways of Changing Dimensions:

- Type - 0
- Type - 1
- Type - 2
- Type - 3

Example: Dimension -> Product

![[Pasted image 20260106172326.png]]

**Use Case:** ->  We need to change the Comb Product Category to Hair.

![[Pasted image 20260106172521.png]]

## Type -1 === UPSERT

	Update + Insert

![[Pasted image 20260106172919.png]]
Here the entire Table is Updated including the existing Records.


**Use Case 2:** ->  We need to change the Comb Product Category to Hair and add New category record.

![[Pasted image 20260106173814.png]]


- If Record exist Update the Record.
- If Record does not exist Insert the Record.
-  Hence -->  `Update + Insert`


## Type - 2  ( History )

We create the Dimension with 3 Columns like Start Date, End Date, In Use



![[Pasted image 20260106175831.png]]

This way the History is Preserved by Keep the old record Date and Use Type



## Type 3 ( Keeping the old records )

We maintain the History by creating a New OLD column

![[Pasted image 20260106180327.png]]

This way the history is preserved by keeping the Old record Column


# Data Lake

- Data Lake stores all types of Data like, Semi Structured, Unstructured Data.

![[Pasted image 20260106181450.png]]

-  Data Warehouse only has Structured Data
-  Also Cheaper than Data Warehouse

## Data Lakehouse

![[Pasted image 20260106181902.png]]


# File Formats

- Row Based 
- Column Based

### **Rows Based:**

- Data is stored record wise
- Optimized for Writing. Transactional Records
- Optimized for OLTP Databases
- File Formats - 


![[Pasted image 20260106182848.png]]



### Column Based: 

- Data is Stored Column wise
- Optimized for Read and Faster Querying of Data.
-  Optimized for OLAP Databases
-  File Formats - Parquet, ORC

![[Pasted image 20260106183124.png]]

Example: 

	SELECT Product_Id FROM ProdDimesion


- Data is read Easily as 1 2 3.

## Delta File Format:

-  Delta File Format is a Parquet File with all the meta data in the Transaction Log which are in JSON Files
-  Advantage -> Data Versioning
-  We can Evolve the Schema ( Schema Evaluation )
-  ACID ( Atomicity, Consistency, Isolation , Durability ) properties 

# Big Data

Real-time Data Streaming - Kafka
Data Pipeline Orchestration - Apache Airflow
Distributed Data Computing - Apache Spark
Cluster Management - Databricks

# Cloud Data Engineering


### Medallion Architecture


![[Pasted image 20260107151342.png]]


-  Bronze Layer - Raw Data Ingestion / Integration
-  Silver Layer - Data Cleaning based Transformation
-  Gold Layer - Data Stored in form of Facts / Tables / Dimensions etc