
![](../../attachments/Pasted%20image%2020260413152354.png)

https://datavidhya.com/learn/cloud-and-infrastructure/cloud-fundamentals/aws-vs-gcp-vs-azure/

![](../../attachments/Screenshot%20from%202026-04-13%2015-34-12.png)

### **AWS Services Stack**

- **AWS S3** :


### Data Sources
- Youtube API
- Kaggle Dataset -> Static Dataset


### AWS S3 Buckets
- ![](../../attachments/Screenshot%20from%202026-04-13%2016-26-55.png)
### Configuring CLI 

- IAM -> USERS ->Select User (suraj)->Security Credentials->Create Access Key
- ![](../../attachments/Screenshot%20from%202026-04-13%2017-43-12.png)
- Download the CSV and Access Key
	- The CSV file will have
		- Access Token
		- Access Key
- Go to the Linux Terminal (prerequisite -> DOWNLOAD AWS CLI)
	- `aws configure`
	- `Enter Token`
	- `Enter Secret Key`
	- `Enter Default Zone`
	- `Can skip`
	- Done
- `aws s3 ls` -> to view the folders
	- ![](../../attachments/Screenshot%20from%202026-04-13%2017-47-11.png)
### Uploading Data to S3

- ![](../../attachments/Pasted%20image%2020260413171939.png)
- **Object**
	- Uploading Any Data like folders/csv.etc in our Bucket is a **Object**
	- An Object has Key : Value Pair
	- **Sample Object Key Value**
		- **Key :** `s3://yt-analytics-pipeline-bronze-ap-south-1/youtube/raw_statistics/region-ca/`
		-  **Value:** `CAVideos.csv`
	- Go to your local Directory
		- ![](../../attachments/Screenshot%20from%202026-04-13%2017-55-13.png)
		- Enter command to copy the CAvideos.csv file to the bucket
			- `aws s3 cp CAvideos.csv s3://yt-analytics-pipeline-bronze-ap-south-1/youtube/raw_statistics/region=ca/`

		- ![](../../attachments/Screenshot%20from%202026-04-13%2017-56-31%201.png)
	- Upload all the files , either 1 by 1 or using script


### Setting Up Roles for Lambda Service

- ##### Roles Service
	- Create a Role -> Service (Select Lambda) ->
	- ![](../../attachments/Pasted%20image%2020260413181201.png)
	- ->Add Required Permission
		- `AWSLambdaBasicExecutionRole`
		- Create Policy - For S3 Buckets
			- ![](../../attachments/Pasted%20image%2020260413182144.png)
			- 
	- ![](../../attachments/Screenshot%20from%202026-04-13%2018-12-56.png)
	- -> Add Role Name
		- ![](../../attachments/Screenshot%20from%202026-04-13%2018-14-08.png)

		- **Sample Policy**	
				`{`
			    `"Version": "2012-10-17",`
			    `"Statement": [`
			        `{`
			            `"Effect": "Allow",`
			            `"Action": [`
			                `"sts:AssumeRole"`
			            `],`
			            `"Principal": {`
			                `"Service": [`
			                    `"lambda.amazonaws.com"`
			                `]`
			            `}`
			        `}`
			    `]`
			`}`
		- `
	- Final Permision
		- ![](../../attachments/Pasted%20image%2020260413182418.png)

### AWS Lambda

- Go to AWS Lambda and create function
	- ![](../../attachments/Screenshot%20from%202026-04-13%2018-28-35.png)
- Select your role
	- ![](../../attachments/Pasted%20image%2020260413182911.png)
- **Updating the Policy of Lambda Service by Updating -> `# s3_lambda_yt_pipeline_policy`**
	- **We are Adding Glue access to our Lambda service**
		- `{`
			`"Sid": "GlueAccess",`
			`"Effect": "Allow",`
			`"Action": [`
				`"glue:GetTable",`
				`"glue:GetDatabase",`
				`"glue:CreateTable",`
				`"glue:UpdateTable",`
				`"glue:GetPartitions",`
				`"glue:CreatePartition",`
				`"glue:BatchCreatePartition"`
				`],`
			`"Resource": "*"`
		`},`
		`{`
			`"Sid": "SNSAccess",`
			`"Effect": "Allow",`
			`"Action": "sns:Publish",`
			`"Resource": "arn:aws:sns:us-east-1:514201995622:yt-analytics-pipeline-*"`
		`}`

	- **We are adding Athena Access**
		- `{`
			`"Sid": "AthenaAccess",`
			`"Effect": "Allow",`
			`"Action": [`
				`"athena:StartQueryExecution",`
				`"athena:GetQueryExecution",`
				`"athena:GetQueryResults"`
				`],`
			`"Resource": "*"`
		`}`

- **FINAL POLICY**
```
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Sid": "S3Accesss",
			"Effect": "Allow",
			"Action": [
				"s3:GetObject",
				"s3:PutObject",
				"s3:ListBucket"
			],
			"Resource": [
				"arn:aws:s3:::yt-analytics-pipeline-bronze-ap-south-1/*",
				"arn:aws:s3:::yt-analytics-pipeline-bronze-ap-south-1/",
				"arn:aws:s3:::yt-analytics-pipeline-silver-ap-south-1/*",
				"arn:aws:s3:::yt-analytics-pipeline-silver-ap-south-1/"
			]
		},
		{
			"Sid": "GlueAccess",
			"Effect": "Allow",
			"Action": [
				"glue:GetTable",
				"glue:GetDatabase",
				"glue:CreateTable",
				"glue:UpdateTable",
				"glue:GetPartitions",
				"glue:CreatePartition",
				"glue:BatchCreatePartition"
				],
			"Resource": "*"
		},
		{
			"Sid": "SNSAccess",
			"Effect": "Allow",
			"Action": "sns:Publish",
			"Resource": "arn:aws:sns:us-east-1:514201995622:yt-analytics-pipeline-*"
		},
		{
			"Sid": "AthenaAccess",
			"Effect": "Allow",
			"Action": [
				"athena:StartQueryExecution",
				"athena:GetQueryExecution",
				"athena:GetQueryResults"
				],
			"Resource": "*"
		}
	]
}
```

**Console:**
	- ![](../../attachments/Screenshot%20from%202026-04-13%2018-56-23.png)


### AWS Glue Role 

- Based on our Architecture we need to give AWS Glue S3 Access from **Bronze and Silver Layer** 
- ![](../../attachments/Screenshot%20from%202026-04-13%2019-16-04.png)
- Roles -> Create Role -> Select Glue
- ![](../../attachments/Screenshot%20from%202026-04-13%2018-58-45.png)
- Create Inline  Policy
- ![](../../attachments/Pasted%20image%2020260413191026.png)

- **AWS Glue Policy**
```
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Sid": "Statement1",
			"Effect": "Allow",
			"Action": [
				"s3:GetObject",
				"s3:PutObject",
				"s3:ListBucket",
				"s3:DeleteObject"
			],
			"Resource": [
				"arn:aws:s3:::yt-analytics-pipeline-bronze-ap-south-1/*",
				"arn:aws:s3:::yt-analytics-pipeline-bronze-ap-south-1/",
				"arn:aws:s3:::yt-analytics-pipeline-silver-ap-south-1/*",
				"arn:aws:s3:::yt-analytics-pipeline-silver-ap-south-1/",
				"arn:aws:s3:::yt-analytics-pipeline-gold-ap-south-1/*",
				"arn:aws:s3:::yt-analytics-pipeline-gold-ap-south-1/",
				"arn:aws:s3:::yt-analytics-pipeline-script-ap-south-1/*",
				"arn:aws:s3:::yt-analytics-pipeline-script-ap-south-1/*"
			]
		}
	]
}
```

 - We are providing S3 access to our Glue Service

### AWS SNS - Simple Notification Service

- SNS provides notification to email or number if the pipeline fails
- Failure Notification

- #### Setting Up Access - Roles
	- Amazon SNS -> Topic -> Create Topic
	- Enter Name and create a Topic
	- In the Topic create subscription
		- ![](../../attachments/Screenshot%20from%202026-04-13%2020-19-27.png)
		- ![](../../attachments/Screenshot%20from%202026-04-13%2020-20-37.png)

- #### Adding the  SNS Resource to our Lambda Policy
	- Copy the SNS ARN
	- `arn:aws:sns:ap-south-1:514201995622:yt-data-pipeline-alerts:012b0f6b-9a7f-4d03-a141-2d07252d0a8b`
	- Open the Lambda service Policy
		- Paste the SNS in the Lambda policy
		- ![](../../attachments/Screenshot%20from%202026-04-13%2020-23-47.png)
		- 

### What is AWS Glue

- Fully managed ETL service
- ETL Jobs in python shell or Pyspark 
- Glue Data Catalog -> centralized metadata repository
- Crawlers -> auto schema from S3 and then we can use SQL queries
- Integerates well with Athena , Redshift , S3 , RDS 

### Amazon EMR

- Managed big data platform
- processing vert large amount of data
- If you need full spark / hadoop control


### Amazon Athena

- Serverless interactive query service
- Standard SQL on S3 data
- It does not own any data , it only runs a query layers
- Gets the data from Glue Data Catalog
-  Pay per query
- Glue Data Catalog as Metastore


## AWS Glue Data Catalog

- It **creates a table _definition_** that _points to data in S3_.
- Glue catalog has the data about the data like schema etc.
- Your data stays in **S3 (unchanged)**
- Glue creates metadata like:
    - Table name
    - Columns
    - Data types
    - File format (CSV, Parquet, etc.)
    - S3 location
- Now you can run SQL queries on your Glue Database
- ##### In S3:
		`s3://orders-data/`  
		   `├── part-1.csv`  
		   `├── part-2.csv`
- ##### Glue Catalog defines:
	Table: orders  
	Columns: order_id, amount, date  
	Location: s3://orders-data/
- #####  Then you run: (with help of athena)
	SELECT * FROM orders;

- All the Above is Done through a **AWS Glue Crawler**

![](../../attachments/Pasted%20image%2020260415234002.png)
### Create Database from AWS Glue Catalog

- ![](../../attachments/Screenshot%20from%202026-04-15%2019-30-13.png)
- **Our Data is stored in a Object Based data storage system called the S3**
- **HDFS is the Hadoop Distributed File System**
- S3 is a Hierarchical type Data storage
	- **Organization:** It structures data hierarchically using files and folders (directories).
- Now we need to convert our data into a **Database Table** for us to run analytics
- #### First we create AWS Glue Database
	- ![](../../attachments/Pasted%20image%2020260415195615.png)
	- Add 3 Database for each layer
		- Bronze
		- Silver
		- Gold
- #### AWS GLUE CRAWLER
	- We will use AWS Glue crawler to pick the data from the S3 , infer the schema do basic steps to transform the Data into a **Database Table**
	- **AWS Glue Crawler** can work with simple CSV , cannot work with complex JSON data
	- Step 1 - Go to crawler tab
		- ![](../../attachments/Screenshot%20from%202026-04-15%2019-59-38.png)
	- 
- 
	- Select Data source as S3 and select the folders
	- The Sub folders will be treated as index
	- ![](../../attachments/Screenshot%20from%202026-04-15%2019-37-23.png)
	- **Create a Crawler**
	- ![](../../attachments/Pasted%20image%2020260415200026.png)
	- Select a Name and Add a Data Source
	- The Data Source will be our S3 - bronze bucket where we uploaded our data
	- Choose the raw_statistics folder
	- ![](../../attachments/Screenshot%20from%202026-04-15%2020-02-52.png)
	- 
	- ![](../../attachments/Pasted%20image%2020260415200130.png)
	- Contents of RAW statistics
	- ![](../../attachments/Screenshot%20from%202026-04-15%2020-04-25.png)
	- NOTE we are only selecting the entire folder -> the sub folders will be treated like index
	- We can create multiple Data source for 1 crawler
	- Finally choose the IAM role ( we already created **Glue IAM role**)
	- Crawler created
		- ![](../../attachments/Screenshot%20from%202026-04-15%2020-08-08%201.png)
	- ![](../../attachments/Screenshot%20from%202026-04-15%2020-07-20.png)
	- 2 Data source - one for **raw_statistics** and one for **raw_reference_data**
	- Now Run the Crawler
		- 
	- **And finally Tables created in the Database**
		- ![](../../attachments/Screenshot%20from%202026-04-15%2019-41-13.png)
- 
	
- #### AWS Athena
- Now to RUN SQL queries on the table , we can use **AWS Athena**
	- ![](../../attachments/Screenshot%20from%202026-04-15%2019-43-55.png)
	- Before Running the AWS Athena, we will create a folder where all the results of the Athena query will be stored
	- Go to Query Settings and then Manage Access
	- ![](../../attachments/Pasted%20image%2020260415201913.png)
	- ![](../../attachments/Pasted%20image%2020260415201957.png)
	- We created a new bucket to store the Athena Query Result
	- **IN AWS Glue Data catalog we can see the schema and Partition of our table**
	- ![](../../attachments/Screenshot%20from%202026-04-15%2020-22-32.png)
	- ![](../../attachments/Screenshot%20from%202026-04-15%2020-23-17.png)


## STAGE 2 - Data from YouTube API - S3 Bronze Bucket


- 

- #### Creating Lambda service to Ingest Data from Youtube API and store it in S3
- Create a lambda service with name
	 `yt-data-pipeline-youtube-ingestion-dev`
- Edit the Environment Variables
- ![](../../attachments/Pasted%20image%2020260417185326.png)
- ###

- ### Getting Youtube API Key 
	- Google Developer Console -  https://console.cloud.google.com/apis/dashboard?project=sturdy-conduit-476214-g0
	- Search for **Youtube Data API v3** ann create API
	- Loading bucket S3 bronze bucket
	- Test - > create a new Test event
		- ![](../../attachments/Screenshot%20from%202026-04-17%2019-00-56.png)
	- ![](../../attachments/Pasted%20image%2020260417190506.png)


- ## STAGE 3 -> Transforming the Data from the S3 Bronze to S3 Silver (JSON -> Parquet)

	- ![](../../attachments/Pasted%20image%2020260417191606.png)
	- **Lambda Function name**
		- `yt-data-pipeline-json-to-parquet-dev`
	- **Environment Variable**
		- ![](../../attachments/Pasted%20image%2020260417191650.png)
	- **Add Trigger**
		- ![](../../attachments/Pasted%20image%2020260417191805.png)
		- **Trigger Settings**
			- ![](../../attachments/Screenshot%20from%202026-04-17%2019-40-39.png)
			- If any objects in the prefix path ending with .json is created , then the tigger/lambda service will get activated
			- The Lambda trigger will fire **only when**:
				1. A new object is created (PUT, POST, COPY, etc.)
				2. The object key (path) **starts with**  
				    `youtube/raw_statistics_reference_data/`
				3. The object key **ends with** `.json`
			- Go to the Code in Lambda service
				- ![](../../attachments/Screenshot%20from%202026-04-17%2019-28-56.png)
				- Add **pandas** Layer as we are using pandas.

	- NOTE : Data not populating in S3 - silver


  ## AWS Glue

   - 