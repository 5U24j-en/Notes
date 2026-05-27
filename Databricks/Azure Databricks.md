

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


## Azure Databricks

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
			- **Catalog** is the main group
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
			 - ##### Now we create the Metastore
			 - ![](../attachments/Pasted%20image%2020260527153317.png)
			 - **ADLS Gen 2 path Format =** ` <container_name>@<storage_account_name>.dfs.core.windows.net/ `
			 - Access Connector ID is found in the Access connector description
			 - After Assigning workspace , which is our Resource Group, now it is connected 


	- ### Creating Compute
		- Creating Compute in Databricks
			- ![](../attachments/Screenshot%20from%202026-05-27%2015-43-42.png)
		- Compute Created by Databricks resource group -> ` sndatabricksrg `:
			- ![](../attachments/Screenshot%20from%202026-05-27%2015-54-48.png)

		- User - surajnair3840@gmail.com
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
		
	- External Location
		- ![](../attachments/Pasted%20image%2020260527165429.png)
		- #### WE need to create new Credentials
		- ![](../attachments/Pasted%20image%2020260527165518.png)
		- The Credentials should have the **Access Connector ID**  ( As per the flow )
		- Now we create **External Location**
		- External Location Should be referenced to the **Container Level.**
			- ![](../attachments/Screenshot%20from%202026-05-27%2017-27-25.png)
			- ` abfss://<container_name>@<storage_account_name>.dfs.core.windows.net/ `
			- 