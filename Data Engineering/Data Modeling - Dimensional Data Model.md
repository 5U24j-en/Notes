
- Data Modeling is the process of creating a blueprint of how data is stored , connected and retrieved in a system

- ### ETL Layers

	- ##### Staging Layer
		- From Multiple Sources to a common Stage
		- No transformation
		- **2 types of Staging**
			- **Persistent**
				- The Data from the Sources are stored in the layer
			- **Transient**
				- Only the new Data from the source is kept . 
				- Similar to overwriting the data every time in the layer.
	- ##### Transformation Layer
		- Transforming the Data 
		- Cleaning Data
	- ##### Serve Layer
		- Building Data Model
	- ![](../attachments/Pasted%20image%2020260503193224.png)
	
	- #### Incremental Loading
		- Only New data should be copied / updated to the tables , we should not copy the entire data from source if new data is added
		- 4,5 is the new data and only that moves through the ETL pipeline
	- #### Staging -> Persistent
		- The entire data from the source is stored
		- Similar to updating the table
		- when new data like 4,5 comes .. 1,2,3 data also exists
	- #### Staging -> Transient
		- Only the new data from the pipeline job is there
		- Similar to overwriting the data everytime
		- only data 4,5 is there in the stage when the new data comes.
	- #### Transform Layer
		- All Data MUST be present in the **TRANSFORM** Layer
		- We will only **UPSERT** the data.
	- ![](../attachments/Pasted%20image%2020260503194220.png)


- ### Medallion Architecture

	- Same Concept , Different Naming Convention
	- ![](../attachments/Pasted%20image%2020260503195136.png)


## Databricks

 - **Workspace** -> Code (processing) 
	- Create a `DataModeling` Folder and create a `Source` Notebook
	- ![](../attachments/Pasted%20image%2020260503204255.png)

- **Catalog** -> Database
	- Create a Catalog `datamodeling` and create **Schemas** (similar to table) -> bronze, silver, gold
	- **NOTE:** `infromation_schema` and `default` are created by default 
	- ![](../attachments/Pasted%20image%2020260503204533.png)

- **Source** -> Notebook
	- We will store the source Data in the `default` Schema /table
	- ![](../attachments/Pasted%20image%2020260503204927.png)

- Bronze -> Notebook
	- We will be implementing Transient Bronze Layers
		- Last Load Date Variable
			- This variable will be configured in the global **ETL** level, but now we will use a temp python variable
			- ![](../attachments/Screenshot%20from%202026-05-04%2019-13-05.png)
			- This Variable will be updated each time we run a ETL pipeline and the records which have value greater than will only be fetched.
	- Creating Temp view of the Updated records from the source
		- ![](../attachments/Screenshot%20from%202026-05-04%2019-13-27.png)
		- WHERE condition will fetch only the records which were added after the last load date
	- Create the TABLE and load values in the Bronze layer
		- ![](../attachments/Screenshot%20from%202026-05-04%2019-15-55.png)

- ### ETL Flow
	- ##### Source 
		- We created a table 
			 `datamodeling.default.source`
		- We will keep on adding data to simulate incremental loading
			- ![](../attachments/Pasted%20image%2020260504221336.png)
	- ##### Bronze
		- **Transient Layer**
			- i.e. The Data will be temporary and will be overwritten each time new data is added
		- **STEP - 1 :** We need  to collect the latest data load date from the bronze table - `datamodeling.bronze.bronze_table`
			- ![](../attachments/Screenshot%20from%202026-05-04%2022-16-30.png)
		- **STEP - 2** : From the source table, we will pull all the rows/records that have dates greater than the latest data load date
			- We will create a temporary view
			- ![](../attachments/Pasted%20image%2020260504221754.png)
		- **STEP - 3:** Add the data to the `datamodeling.bronze.bronze_table` Table
			- ![](../attachments/Pasted%20image%2020260504221841.png)
		- ![](../attachments/Pasted%20image%2020260504225224.png)
		
	- ##### Silver 
		- Collect the entire table from the `datamodeling.bronze.bronze_table` Table
		- Transform the Data from the table 
			- Create new column - `date(current_timestamp()) as process_date`
			- Upper case the Name
		- Create Temp view - `silver_source`
		- ![](../attachments/Pasted%20image%2020260504224939.png)
		- **UPSERT**
			- ![](../attachments/Pasted%20image%2020260504225113.png)
			- **`ON datamodeling.silver.silver_table.order_id = silver_source.order_id`** - order_id -> Primary Key
			- **`WHEN MATCHED THEN UPDATE SET *`**
				- If a record already exists → **UPDATE it**
				- If `order_id` is new
				- Then insert full row
			- **`WHEN NOT MATCHED THEN INSERT *`**
				- If it doesn’t exist → **INSERT it**
				This is called an **UPSERT**.




![](../attachments/Pasted%20image%2020260504224611.png)