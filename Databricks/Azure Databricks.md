

- ## Resource Group

	- Similar to a Folder
	- #### Resource
		- Resource is any Service in Azure
	- All the **Resources** will be under a **Resource Group**
	- Resources we are going to create
		- Azure Databricks
		- Storage Account
			- Blob Storage
			- Tables
			- File Shares
			- Queues
		- ![](../attachments/Screenshot%20from%202026-05-26%2017-49-24.png)
		- ![](../attachments/Screenshot%20from%202026-05-26%2017-51-07.png)
		- Resource Group Create  - ` data-pipeline-v1 `
		- Select the Resource Group and then select **Storage Account.**
			- ![](../attachments/Screenshot%20from%202026-05-26%2017-52-33.png)
			- Options
				- ![648](../attachments/Screenshot%20from%202026-05-26%2017-55-32.png)
				- **Enable Hierarchical Namespace creates the Data Lake**
					- ![](../attachments/Pasted%20image%2020260526180054.png)
	- Adding Databricks
		- Under Resource Group Add resource and search for Databricks
		- ![](../attachments/Pasted%20image%2020260526181858.png)
		- Control Plane is the UI
		- Compute Plane is the Data-bricks , Managing the Resources like clusters , etc
		- ![](../attachments/Pasted%20image%2020260526182050.png)
		- Create Resource and launch Databricks


## Azure Data-bricks

- ![](../attachments/Pasted%20image%2020260526183533.png)
- First we Create Compute
- **This is the Control Plane**

- ### Unity Catalog

	- You can govern all the credentials, lineage, audits, data tags , etc
	- ![](../attachments/Screenshot%20from%202026-05-26%2018-40-59.png)

	- #### Metastore

		- Metadata: Data about data
		- All the Metadata is stored under **Metastore**
		- With Unity Catalog , all the information in Unity Catalog is stored in 1 Metastore
			- You need to attach the Databricks workspace
		- **Unity Catalog Object Model**
			- 
			- ![](../attachments/Pasted%20image%2020260526191831.png)
			- **Metastore -** Unity Metastore
				- You can only create 1 Metastore in 1 region
				- First Databricks Workspace is created and then it is connected to the Unity Metastore
					- Hive is the legacy Metastore
					- ![](../attachments/Pasted%20image%2020260526192759.png)
			- **Catalog** is the group
				- **Schema** is like a DB
					- Tables
					- Views
					- **Function** - Data-science Models
					- Volumes
						- Volumes can be managed with Unity Catalog
	- ### Hands - On
		- We need to Open Databricks Console
			- accounts.azuredatabricks.net
			- We need to add Microsoft Extra ID
			- **Catalog and create Metastore**
				- ![](../attachments/Screenshot%20from%202026-05-26%2021-57-07.png)
			- When you create Managed Tables, the Databricks will write data in that storage account
				- We have created a storage account
				- Now we will create a container in the storage account and attach it to metastore
				- ![](../attachments/Pasted%20image%2020260527111828.png)
				- Access Connector Id
		- ### Container - Under Storage Account
			- ![](../attachments/Pasted%20image%2020260527112503.png)
			- **Structure**

						Azure Subscription
						   └── Storage Account
								 └── Container
									   └── Blobs (files)

			- Example:

					 Storage Account: companydatalake
						   ├── raw-data
						   │      ├── sales_2026.csv
						   │      ├── customer.json
						   │
						   ├── processed-data
						   │      ├── cleaned_sales.parquet
						   │
						   └── logs
								  ├── app.log

			- **We have created 2 containers**
```json
"Containers": {
"sn-metastore-root": "databricks metastore container",
"data-container": "Azure Account container"
}
```
- 
	- ### Access Connectors
		- **Access connector will be used to connect our storage containers to the Databricks Unity Catalog**
		- ![](../attachments/Screenshot%20from%202026-05-27%2011-46-40.png)
		- We need to add storage account access to our **Access connector**
		- Open storage Account and select Access Control ( IAM )
			- ![](../attachments/Pasted%20image%2020260527115035.png)
		- ![](../attachments/Pasted%20image%2020260527115221.png)
		- Select the Role > **Storage Blob Data Contributor**
			- ![](../attachments/Pasted%20image%2020260527115420.png)
			- We need to select **Managed Identity**
			- #### IAM
				- Users
					- Individual user
				- Groups
					- Group of users have the access
				- Service Principle - Non Human Access
					- External services having access to the Storage
				- Managed Identity - Non Human Access
					- Azure Managed service getting the access, this access credentials are managed by Azure

		 - ### Now we create the Metastore In Databricks
		 
			 - ![](../attachments/Pasted%20image%2020260527153317.png)
				 -  **ADLS Gen 2 path Format =** ` <container_name>@<storage_account_name>.dfs.core.windows.net/ `
				 - Access Connector ID is found in the Access connector description
				 - After Assigning workspace , which is our Resource Group, now it is connected 

			 - ![](../attachments/Pasted%20image%2020260529150654.png)

			 - #### Managed Tables
				 - The Metastore will hold the Metadata of the Managed tables
					 -  Unity Catalog (Metastore) stores the **metadata** of managed tables.
					- The actual table data is stored in the **managed location** configured for the metastore, catalog, or schema (typically an ADLS Gen2 container).
				 - The Main Difference between Managed table and External table is that
					 - If we delete a managed table in Databricks workspace , the Metadata AND the DATA itself is Deleted from the container
					 - If we delete a External Table only the Meta data is deleted.

			- #### External Table
				- Unity Catalog manages only the metadata.
				- The data already exists in an external storage location.
				- If an external table is dropped: Only the metadata is deleted



	- ### Creating Compute
		- Creating Compute in Data-bricks
			- ![](../attachments/Screenshot%20from%202026-05-27%2015-43-42.png)
		- Compute Created by Data-bricks resource group -> ` sndatabricksrg `:
			- ![](../attachments/Screenshot%20from%202026-05-27%2015-54-48.png)

		- User - suraj@surajnair3840gmail.onmicrosoft.com
			-  Workspace
				- **Folder -**  sn-data-pipeline
					- Create Notebook

		- **Magic Commands -** which language to use in Notebook cell
			- %sql
			- %python
			- %md
			- %run -> Running other notebooks by providing path
			- %fs - File system

		- #### External Location

			- An **External Location** in Unity Catalog is a **named pointer to a path in Azure Data Lake Storage (ADLS Gen2)**, along with the credentials needed to access it.
			- This is a **Unity Catalog object** inside Databricks.
			- Its job is to register a storage path for governed access.
			-  ` External Location = Storage Path + Authentication + Governance `
			- Without Unity Catalog, Databricks could access storage directly:
					` spark.read.parquet("abfss://bronze@mydatalake.dfs.core.windows.net/data/") ``
			
			- But this has problems:
				- everyone can hardcode storage paths
				- weak governance
				- no centralized permissions
				- messy credential handling
				
			- Unity Catalog fixes this.
				- Instead of direct access:
					- ` Users → Unity Catalog → External Location → ADLS `

			- ![](../attachments/Pasted%20image%2020260527164250.png)

			- #### **Access Connector vs External Location**

| Feature                                 | Access Connector                 | External Location                   |
| --------------------------------------- | -------------------------------- | ----------------------------------- |
| **What is it?**                         | Azure resource                   | Unity Catalog object                |
| **Lives where?**                        | Azure Portal                     | Databricks Unity Catalog            |
| **Primary purpose**                     | Authenticate Databricks to ADLS  | Register and govern a storage path  |
| **Contains storage path?**              | No                               | Yes                                 |
| **Contains permissions?**               | Azure RBAC permissions           | Unity Catalog permissions           |
| **Uses managed identity?**              | Yes                              | Indirectly (via Storage Credential) |
| **Created in**                          | Azure Portal / ARM / Terraform   | Databricks SQL / Catalog Explorer   |
| **Knows which folder/path?**            | No                               | Yes                                 |
| **Used for authentication?**            | Yes                              | No (relies on Storage Credential)   |
| **Used for governance/access control?** | No                               | Yes                                 |
| **Example**                             | `unity-catalog-access-connector` | `bronze_ext`                        |
| **Analogy**                             | Security badge                   | Registered room with access rules   |

- 
	- 
		- ### Flow 

				Databricks User
					↓
				Unity Catalog
					↓
				External Location
					↓
				Storage Credential
					↓
				Access Connector (Managed Identity)
					↓
				Azure Data Lake Storage (ADLS Gen2)
		
	- ### External Location
		- ![](../attachments/Pasted%20image%2020260527165429.png)
		- #### WE need to create new Credentials
		- ![](../attachments/Pasted%20image%2020260527165518.png)
		- The Credentials should have the **Access Connector ID**  ( As per the flow )
		- Now we create **External Location**
		- External Location Should be referenced to the **Container Level.**
			- ![](../attachments/Screenshot%20from%202026-05-27%2017-27-25.png)
			- ` abfss://<container_name>@<storage_account_name>.dfs.core.windows.net/ `
			- External Location Created

	- ### Managed Catalog
		
		- Managed Schema
		- Managed table
			- Data is stored in the Metastore managed Table 
				- **Storage Account:** `sndatabrickspipeline`
				- **Container:** ` sn-metastore-root `
			- Structure
				- Catalog
					- Schema ( similar DB )
						- Table
		- CODE

**Catalog**
```sql
CREATE CATALOG managed_catalog;
```

**Schema**
```sql
CREATE SCHEMA managed_catalog.managed_schema;
```

**Table**
```sql
CREATE TABLE managed_catalog.managed_schema.managed_table
(
	id INT,
	name STRING
)
USING DELTA;
```
- 
	- 
		- The Data is stored in the - ` sn-metastore-root `
		- ![](../attachments/Pasted%20image%2020260529162742.png)
		- ![](../attachments/Pasted%20image%2020260529162841.png)
		- **Empty Delta Table**

	- ### External Catalog
	
		- Managed Schema
		- Managed Table
			- Data will be stored in Our container ( External Location )
				- **Storage Account:** ` sndatabrickspipeline `
				- **Container:** ` data-container `
		- CODE

External Catalog
```sql
CREATE CATALOG ext_catalog
MANAGED LOCATION 'abfss://data-container@sndatabrickspipeline.dfs.core.windows.net/external_catalog'
```

Managed Schema
```sql
CREATE SCHEMA ext_catalog.managed_schema;
```

Managed Table
```sql
CREATE TABLE ext_catalog.managed_schema.managed_table2
(
	id INT,
	name STRING
)
USING DELTA;
```
- 
	- 
		- The data is stored in - ` data-container `
		- ![](../attachments/Pasted%20image%2020260529164010.png)
		- ![](../attachments/Pasted%20image%2020260529164108.png)

		- #### External Schema - 
```sql
CREATE SCHEMA ext_catalog.external_schema
MANAGED LOCATION 'abfss://data-container@sndatabrickspipeline.dfs.core.windows.net/external_schema';
```
- 
	- 
		- Data is still stored in the ` data-container `
		- ![](../attachments/Pasted%20image%2020260529164817.png)

- **NOTE: External Table if defined , will be stored in that defined location , And rest , Catalog and Schema will be in the same location**
```sql
CREATE TABLE managed_catalog.managed_schema.managed_table3
(
	id INT,
	name STRING
)
USING DELTA
LOCATION `abfss://data-container@sndatabrickspipeline.dfs.core.windows.net/external_table/table4`
```
- Location
	- ![](../attachments/Pasted%20image%2020260529165454.png)
	- ![](../attachments/Pasted%20image%2020260529165514.png)


### Dropping Table

	- Dropping Managed table from Managed schema and managed catalog
	- Storage location - ` sn-metastore-root `

```sql
DROP TABLE managed_catalog.managed_schema.managed_table;
```
- 
	- #### Managed tables are deleted after 7 days
	- We can Un-drop / Restore the tables as well
```sql
UNDROP TABLE managed_catalog.managed_schema.managed_table;
```

### Inserting Data

```sql
INSERT INTO managed_catalog.managed_schema.external_table
VALUES
(1, 'abc'),
(2, 'test'),
(3, 'okay'),
(4, 'Data')
```

### Directly Querying Data

```sql
SELECT * FROM delta.`abfss://data-container@sndatabrickspipeline.dfs.core.windows.net/external_table/table4`;
```

### Permanent Views

```sql
CREATE VIEW managed_catalog.managed_schema.view1
AS
SELECT * FROM delta.`abfss://data-container@sndatabrickspipeline.dfs.core.windows.net/external_table/table4`
WHERE id = 1;
```
- ![](../attachments/Pasted%20image%2020260529171524.png)

### Temporary Views

```sql
CREATE OR REPLACE TEMP VIEW temp_view
AS
SELECT * FROM delta.`abfss://data-container@sndatabrickspipeline.dfs.core.windows.net/external_table/table4`
WHERE id = 1;
```
- These Views will go away  once compute is detached


## Volumes

- Volumes are Unity Catalog objects representing a logical volume of storage in a cloud object storage location
- While Tables provide governance over tabular datasets, **Volumes add governance over non-tabular datasets**
- Volumes can be used to access files in any format, including structured, semi-structured and unstructured data.
- **Path Format**:
	- ` /Volumes/<catalog_name>/<schema_name>/<volume_name>/<path>/<file_name> `

- ##### Creating a Directory - ` dbutils.fs.mkdirs('abfss://data-container@sndatabrickspipeline.dfs.core.windows.net/Volumes') `
	- We have created a new Folder -  ` Volumes ` in the container - ` data-container ` , storage account - ` sndatabricks ` 
	- We have also manually created a **` source `** folder which has a file Sales
		- ![](../attachments/Pasted%20image%2020260531183441.png)

- ### External Volume

```sql
CREATE EXTERNAL VOLUME managed_catalog.managed_schema.myvolume
LOCATION 'abfss://data-container@sndatabrickspipeline.dfs.core.windows.net/Volumes'
```
- **Result**
	- ![](../attachments/Pasted%20image%2020260531183908.png)

- ##### Copying Files - ` dbutils.fs.cp('sources_path', 'destination_path') `
	- Sample - ` dbutils.fs.cp('abfss://data-container@sndatabrickspipeline.dfs.core.windows.net/source/Sales', 'abfss://data-container@sndatabrickspipeline.dfs.core.windows.net/Volumes/Sales')  `	
	- ![](../attachments/Pasted%20image%2020260531184538.png)

- Now since we have Created a Volume and Added the required paths to Data-bricks Catalog, we can now query and govern the unstructured data in Volumes
```sql
SELECT * FROM csv.`/Volumes/managed_catalog/managed_schema/myvolume/Sales`
```
- **Output**
	- ![](../attachments/Pasted%20image%2020260531184938.png)


## Delta Lake

- Creating External Table under Managed Catalog and Managed Schema
- We created a new folder :-  deltatable in the same container - ` data-container ` 
```sql
CREATE TABLE managed_catalog.managed_schema.delta_table1
(
	id INT,
	name STRING,
	city STRING
)
USING DELTA
LOCATION 'abfss://data-container@sndatabrickspipeline.dfs.core.windows.net/deltatable/deltatbl1'
```
- ![](../attachments/Pasted%20image%2020260531190459.png)
- ### **Inserting Data**
```sql
INSERT INTO managed_catalog.managed_schema.delta_table1
VALUES
(1, 'aa', 'delhi'),
(2, 'bb', 'mumbai'),
(12, 'cc', 'moscow'),
(13, 'dd', 'bengaluru'),
(14, 'ee', 'texas'),
(15, 'ff', 'paris'),
(16, 'gg', 'london'),
(17, 'hh', 'singapore'),
(18, 'ii', 'macau'),
(19, 'jj', 'shangai'),
(10, 'kk', 'cincinati')
```
- Getting Extended Data of the Table
```sql
DESCRIBE EXTENDED managed_catalog.managed_schema.delta_table1
```
- Output
	- ![](../attachments/Pasted%20image%2020260531191827.png)
- ###  UPDATING TABLE ( DML )
```sql
UPDATE managed_catalog.managed_schema.delta_table1
SET city = 'toronto'
WHERE id = 10
```

- ### Viewing Versions
```sql
DESCRIBE HISTORY managed_catalog.managed_schema.delta_table1
```
- 
	- ![](../attachments/Pasted%20image%2020260601164555.png)


- ### Time Travel
```sql
RESTORE managed_catalog.managed_schema.delta_table1 TO VERSION AS OF 2
```

- ### OPTIMIZE 
```sql
OPTIMIZE managed_catalog.managed_schema.delta_table1
```

- ### DEEP CLONE vs SHALLOW CLONE
	- ##### Deep Clone
		- It clones the Meta Data and Data
```sql
CREATE TABLE managed_catalog.managed_schema.deep_clone
DEEP CLONE managed_catalog.managed_schema.delta_table1
```
- 
	- ##### Shallow Clone
		- It only clones the meta data
		- Only delta log is copied
```sql
CREATE TABLE managed_catalog.managed_schema.deep_clone
SHALLOW CLONE managed_catalog.managed_schema.delta_table1
```

- ### AUTO - LOADER
	- Incremental Loading
	- Checkpoint
		- schema or source
		- checkpoint with folder name as source ( in the destination container )

```python
from pyspark.sql.functions import *

df = spark.readStream.format('cloudfiles') \
		.option('cloudFiles.format', 'parquet') \
		.option('cloudFiles.schemaLocation', 'abfss://data-container@sndatabrickspipeline.dfs.core.windows.net/autosink/check') \
		.load('abfss://data-container@sndatabrickspipeline.dfs.core.windows.net/autosource')

df.writeStream.format('parquet') \
	.option('checkpointLocation', 'abfss://data-container@sndatabrickspipeline.dfs.core.windows.net/autosink/check') \
	.trigger(processingTime='10 seconds') \	
	.start('abfss://data-container@sndatabrickspipeline.dfs.core.windows.net/autosink/data')
```

Output:
	![](../attachments/Pasted%20image%2020260601171720.png)
```json
{
  "id" : "d719df94-9deb-4266-b121-f32ef4e35350",
  "runId" : "9e287906-4a15-4936-97b6-ec87e3c54617",
  "name" : null,
  "timestamp" : "2026-06-01T11:47:30.001Z",
  "batchId" : 1,
  "batchDuration" : 3,
  "numInputRows" : 0,
  "inputRowsPerSecond" : 0.0,
  "processedRowsPerSecond" : 0.0,
  "durationMs" : {
    "latestOffset" : 2,
    "triggerExecution" : 3
  },
  "stateOperators" : [ ],
  "sources" : [ {
    "description" : "CloudFilesSource[abfss://data-container@sndatabrickspipeline.dfs.core.windows.net/autosource]",
    "startOffset" : {
      "seqNum" : 3,
      "sourceVersion" : 3,
      "lastBackfillStartTimeMs" : 1780314325655,
      "lastBackfillFinishTimeMs" : 1780314330787,
      "lastInputPath" : "abfss://data-container@sndatabrickspipeline.dfs.core.windows.net/autosource"
    },
    "endOffset" : {
      "seqNum" : 3,
      "sourceVersion" : 3,
      "lastBackfillStartTimeMs" : 1780314325655,
      "lastBackfillFinishTimeMs" : 1780314330787,
      "lastInputPath" : "abfss://data-container@sndatabrickspipeline.dfs.core.windows.net/autosource"
    },
    "latestOffset" : null,
    "numInputRows" : 0,
    "inputRowsPerSecond" : 0.0,
    "processedRowsPerSecond" : 0.0,
    "metrics" : {
      "isBacklogComputationComplete" : "true",
      "numBytesOutstanding" : "0",
      "numFilesOutstanding" : "0"
    }
  } ],
  "sink" : {
    "description" : "FileSink[abfss://data-container@sndatabrickspipeline.dfs.core.windows.net/autosink/data]",
    "numOutputRows" : -1
  }
}
```

- This task will keep on running
- If we upload a new file , it will copy that file as well

## Jobs and Pipelines

- ![](../attachments/Pasted%20image%2020260601172344.png)
- ![](../attachments/Pasted%20image%2020260601172648.png)
- ![](../attachments/Pasted%20image%2020260601172705.png)
- 