
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






