

### Elastic Load Balancing & Auto Scaling Groups

- Scalability and High Availability
	- Scalability - ability to handle greater loads
	- Scalability Types
		- Vertical Scalability - Scale up/down
			- Higher Device Specs
		-  Horizontal Scalability - Scale out/in
			- More Machines
			- Auto Scaling Groups
			- Load Balancer
	
	- High Availability
		- Application is running in **at least 2 Availability Zones**.
		- Ability to survive any Disaster

	- ![[Pasted image 20260205152804.png]]


### Elastic Load Balancer - ( ELB )

- Load Balancers are servers that forward internet traffic to multiple servers ( EC2 instances ) downstream.
- ![[Pasted image 20260205153209.png]]
- Advantages of Load Balancers
	- We can spread the user traffic load across multiple downstreams.
	- Expose a single point of access ( DNS ) to your application
	- Handle failures Downstream
	- Can perform regular health checks to your instances

- ELB services is a AWS managed service
	- AWS takes care of upgrades ,maintenance, high availability.
	- AWS provides only a few configuration

- It is cheaper to configure your own load balancer but high level of efforts and maintenance

- 3 types of Load Balancer by AWS
	- Application Load Balancer ( HTTP/ HTTPS ) - Layer 7
	- Network Load Balancer ( High Performance and TCP ) - Layer 4
	- Gateway Load Balancer - Layer 3
- ![[Pasted image 20260205154233.png]]


### Auto Scaling Group ( ASG )

- Goal

	- Scale Out - EC2 Instances - To match an increase load.
	- Scale In - EC2 Instances - To match the decreased load.
	- To ensure that minimum and maximum number of machines are running.
	- Automatically register new instances to a load balancer
	- Can replace unhealthy instances

- #### Auto Scaling Strategies

	- **Manual Scaling** : 
		- In the **ASG configuration**, you define:
			- **Minimum capacity** – lowest number of instances that must always run
		    - **Maximum capacity** – upper limit of instances
		    - **Desired capacity** – actual number of running instances    
		- When scaling manually, you **update the desired capacity** (or min/max values).

	- **Dynamic Scaling**
		- Responds to the changing demand of the traffic
			- Simple / Step Scaling
				- When a CloudWatch ( monitoring service ) alarm is triggered ( ex CPU>70% for x mins), then add 2 units.
			- Target Tracking Scaling
				- Setting a average target of usage
				- example - Average ASG CPU to stay around 40%
			- Scheduled Scaling
				- Scheduling auto scaling based on know usage pattern
				- example - increase min capacity from 10 to 5 pm of Friday
			- Predictive Scaling
				- Uses ML ( Machine Learning ) to predict future traffic ahead of the time and automatically provisions EC2 instances


### Amazon S3

- Use Cases
	- Backup and Storage
	- Disaster Recovery
	- Archive
	-  Hybrid Cloud Storage
	-  Application hosting
	-  Media hosting
	-  Data lakes 
	-  Static websites
	-  Software delivery

- **Objects** - files
- **Buckets** - directories 
- Buckets must a globally unique name. ( across all regions )
- Buckets are defined at the **Region Level** .

- ###### Objects
	- Objects have a key.
	- key is the full path 
		- ![[Pasted image 20260205165525.png]]
	- key has **prefix + object name**
	- max object size - 5000 GB ( 5 TB )
	- Objects have Metadata 
	- Tags -  Unicode key / value pair -> for security
	-  Version ID - ( versioning of S3 buckets )


- #### Security

	-  **Account - Based**
	
		- **IAM Policies** 
			- which API call should be allowed for a specific user from IAM.
			- ![[Pasted image 20260205182031.png]]
			
		- **IAM Roles - for services**
			- ![[Pasted image 20260205182121.png]]


	- **Resource -Based**
	
		- **Bucket Policies** 
		
			- bucket wide rules from the S3 console 
			- JSON based Policies
				- ![[Pasted image 20260205181214.png]]
				- Resource: 
					- buckets or objects
					- `arn:aws:s3:::examplebucket/*`
					- `/*` - means all objects in this `examplebucket`
				- Effect:
					- Allow/Deny
				- Actions:
					- Set of API to allow or deny
					- `s3:GetObject`
				- Principle:
					- The account or user to apply the policy to
					- `"*"` mean all
				- ![[Pasted image 20260205181922.png]]

			- Cross Account Access
				- For IAM user from a different AWS Account
				- ![[Pasted image 20260205182310.png]]


		- **Object Access Control List ( ACL )**
			- Object level control

		- **Bucket Access Control List**
			-  Bucket level control


	- We can encrypt objects in S3 using encryption keys


- #### S3 - Versioning

	- Creating a copy version of your buckets when any changes are made to prevent permanent delete.
	- If we overwrite any file , i will change version like , 1, 2 ,3. etc
	- ![[Pasted image 20260205183434.png]]
	- Advantages
		- Protect against unintentional deletes
		-  Easy roll back to previous versions


- #### S3 - Replication ( CRR and SRR )

	- To Enable Replication we need to enable Versioning

	-  CRR - Cross Region Replication
		- Use Case
			- Compliance
			- Lower Latency access
			- Replication across accounts


	- SRR - Same Region Replication
		- Use Case
			- Log aggregation
			- Live Replication between production and test accounts


- #### S3 Durability and Availability

	- Durability :
		- High Durable 99.999... (11 9s) % across multiple AZ

	- Availability:
		- Highly Available - 99.99%
		-  Service is only down 53 mins a year.

- #### S3 Storage Class

	- **S3 Standard - General Purpose**
		- Used for frequently accessed Data.
		- Low latency and high throughput
		-  Use cases
			- Big Data analytics
			- Mobile & Gaming application
			- CDN

	- **S3 Standard IA -  Infrequent Access**
		- Less frequently accessed Data
		- Rapid access when needed
		- Use cases
			- Disaster Recovery
			- Backups
	-  **S3 One Zone - IA - Infrequent Access**
		- High durability in single Zone
		- Use cases
			- Storing secondary Backups for data you can recreate

	-  **S3 Glacier Instant Retrieval**
		- Low - cost object storage for archiving / backup
		- Price for storage + object retrieval cost
		-  Use Cases
			- Millisecond Retrieval , for Data accessed once a quater
			- Minimum storage of 90 days.

	- **S3 Glacier Flexible Retrieval**
		- Data Retrieval Duration
			- Expedited ( 1 to 5 mins )
			- Standard ( 3 to 5 hours )
			- Bulk ( 5 - 12 hours )
			- Minimum storage of 90 days

	-  **S3 Glacier Deep Archive**
		- Data Retrieval Duration
			- Standard ( 12 hours )
			- Bulk (48 hours )
			- Minimum storage of 180 days

	-  **S3 Intelligent Tiering** 
		- Small monthly monitoring and auto - tieting fees
		- Moves objects automatically between Access Tiers based on usage
	
	![[Pasted image 20260208140506.png]]


	- **S3 Express One Zone**
		-  High Performance , **single Availability Zone** Storage class
		-  Objects are stored in a Directory Bucket
		-  10X better performance than S3 Standard with 50% lower cost.
		-  Handles - 100000s requests per sec with single digit latency.
		- Use Case
			- Latency sensitve apps
			- Data intensive apps
			- AI & ML training
			- financial Modelling
			- HPC
			- Best with SageMaker Model Training
			- Athena 
			- EMR 
			- Glue

- #### S3 Encryption 

	- Types of Encryption

		- Server Side Encryption ( Default )
			- Server encrypts file once received in the Bucket
			- ![[Pasted image 20260208141512.png]]
		
		- Client Side Encryption
			- ![[Pasted image 20260208141535.png]]


- #### IAM Access Analyser for S3

	- Ensures only intended people have access to your S3 bucket.
	- Evaluates
		- S3 bucket Policies
		- S3 ACLs
		- S3 Access point Policies


- #### Shared Responsibility Model - S3

	- ![[Pasted image 20260209152313.png]]


### AWS Snowball

- NOTE: Snowball Edge is Discontinued
- A Physical Device to transport Data.
- Highly secure , portable devices to collect and process data at the edge and migrate data into and out of AWS.
- Helps migrate up to Petabytes of Data.
- ![[Pasted image 20260209152629.png]]


- ##### EDGE Computing
	- A Physical Computing Device that is mobile ( eg. CPU on Trucks, etc)


### Hybrid Cloud for Storage - Storage Gateway

-  S3 storage is a proprietary storage technology - UNLIKE **EFS / NFS**
-  Hence Using Hybrid storage the Data File Management system changes.
-  To bridge between that we use **Storage Gateway**.
-  Types of Storage Gateway
	- File Gateway
	- Volume Gateway
	- Tape Gateway
- ![[Pasted image 20260209153630.png]]


# Databases 

- ### Relational Databases - OLTP ( Online Transactional Processing)

	- AWS Managed Databases Advantages
		- Quick Provisioning, High Availability, Vertical and Horizontal Scaling
		-  Automated Backups, Restore , Operations, Upgrades
		-  OS Patching handled
		-  Monitoring and Alerting

	- You can run your own Database Technologies on EC2 Instance but it should be self managed 
		-  Resiliency
		-  Backups
		-  Patching
		-  High Availability
		-  etc


	- #### RDS - Relational Database Service

		- Managed DB service

		-  Uses SQL

		-  It allows you to create databases in the cloud that are managed by AWS
			- Postgre
			- MySQL
			- MariaDB
			- Oracle
			- Microsoft SQL Server
			- IBM DB2
			- Aurora ( AWS DB )

		- Advantages
			-  Automatic provisioning , OS patching
			-  Continuous Backups and restores
			-  Monitoring Dashboard
			- Multi AZ setup for Disaster Recovery
			- Maintenance windows for upgrades
			-  Scaling Capability
			- Storage backed by EBS.

		- YOU CANNOT USE SSH

		- Architecture
			- ![[Pasted image 20260209161506.png]]


		- ##### Amazon Aurora
	
			- Amazon own DB
			-  PostgreSQL and SQL are both supported
			-  It is cloud optimized and claims 5X performance improvement over MySQL on RDS.
			- Storage Automatically Grows.
			- **Aurora Serverless** - No Management. 

		- **Read Replicas:**
			- Scale your read workloads of DB.
			- Can create upto 15 Read Replicas
			-  Data is written to the Main DB.
			- ![[Pasted image 20260209162643.png]]

		-  **Multi - AZ**
			- Replica is created in another Zone
			- Fail-over if there is any AZ outage
			- ![[Pasted image 20260209162842.png]]

		- **Multi - Region ( Read Replicas )**
			- Replicas setup at different Regions
			- Use Case
				- Disaster Recovery
				-  Less Latency based on Region
			- ![[Pasted image 20260209163024.png]]

	- #### Elasti-Cache 

		- This is a Managed Redis or Memcached Service.
		- Caches are in-memory databases wit high performance, low latency.
		- Helps to reduce load off databases for Read intensive workloads.
		- ![[Pasted image 20260209163350.png]]


- ### Non - Relational Databases - OLTP

	- NoSQL databases

	- #### Dynamo DB

		- Fully Managed Highly Available Service with replication across 3 AZ
		- Scales to massive workloads, uses distributed **server-less Databases**
		-  Fast and consistent performance
		- Single - digit ms Latency - Low Latency Retrieval
		- IAM - security
		- Auto Scaling capabilities
		- Standard and IA ( Infrequent Access ) Table Class

		-  Key Value Database
			- ![[Pasted image 20260209163949.png]]

		- We can use Global Tables
			- DBs are replicated in different Regions with Active - Active configurations
			- Based on users location the DB updates will be performed to the DB closer to the User
			- This Reduces latency
			- ![[Pasted image 20260209164657.png]]

	- #### Dynamo DB Accelerator - DAX ( in memory cache )

		- Fully managed in-memory cache
		- 10X performance - microseconds


- ### OLAP Databases (Online Analytical Processing )

	- Used for Analytics / Data processing 
	- Optimized for Fast Reads
	- Data Warehousing

	- #### Redshift

		- Based on PostgreSQL
		- Load Data once every hour.
		- Data Stored in Columns
		- Massively Parallel Query Execution
		- Has SQL interface for performance queries.
		- **Redshift Server-less**
			- Automatic Capacity Scalling
			-  Pay for Use

	- ### EMR - Elastic Map Reduce

		- Elastic Map Reduce
		- EMR Helps in creating Hadoop clusters ( Big Data ) to analyze and process vast amount of Data.
		- The clusters can be made of Hundreds of EC2 instances
		- Also Support Apache Spark, Hbase, Presto, Flink.
		- EMR takes care of all the provisioning and configurations.
		- Auto - scaling and integrated with Spot instances
			- Big Data Processing
			- AI/Ml

	- #### Amazon Athena

		- Server-less Query service to perform analytics against S3 Objects.
		- SQL language to query the files.
		- ![[Pasted image 20260209165814.png]]
		

	- #### QuickSight

		- Server-less ML powered Business Intelligence service to create Dashboards.


	- #### Document DB

		- Amazon Service similar and compatible to MongoDB.

	- #### Neptune

		- Fully Managed Graph Database.
		- Graph Dataset = Social Network

	- #### Timestream

		- DB for a Time-series Dataset.

	- #### Amazon Managed Blockchain

		- A service to create a private or public blockchains

	- #### AWS Glue

		- **ETL service** 
		- For preparing Data for Analytics
		- It is like a AWS version of Spark - ( Glue is build on Spark )
		- ![[Pasted image 20260209170945.png]]


- ### DMS - Database Migration Service

	- Quickly and securely migrate databases to AWS, resilient and self healing
	- Supports
		- Homogeneous Migration
			- Example: Oracle to Oracle Migration
		- Heterogeneous Migration
			- Example: MySQL to Aurora
	
- ![[Pasted image 20260210210029.png]]


### Docker

- Apps are  packaged in containers that can run on any OS.
-  Apps can run the same, regardless of where they run
-  Docker Images are stored in Docker Repositories
-  **Private Docker Respositroy**
	- **ECR**

- Docker versus Virtual Machines
	- Resources are shared by the Host
	- **VM** 
	- ![[Pasted image 20260210210740.png]]
	- **Docker**
	- ![[Pasted image 20260210210805.png]]


### ECS - Elastic Container Servcie

- Launch Docker containers on AWS
- You must provision and maintain the Infrastructure ( on EC2 Instance )
-  AWS takes care of starting / stopping container
- ![[Pasted image 20260210211041.png]]

### Fargate

- Launch Docker containers on AWS
- No need to Provision infrastructure like EC2.
- Serverless offering
- AWS runs the containers based on the CPU/RAM you need.
- ![[Pasted image 20260210211208.png]]

### ECR - Elastic COntainer Registry

- Private Docker Registry on AWS
- Yon can store your Docker images so that they can be run by ECS or Fargate
- ![[Pasted image 20260210211346.png]]


### Amazon EKS - Elastic Kubernetes Service

- Allows you to launch managed Kubernetes clusters on AWS
- Containers can be hosted on:
	- EC2
	- Fargate


## Serverless

- No need to manage Infrastructure.
-  You can just deploy code.
-  Faas - Function as a service.
- Example
	- ![[Pasted image 20260210211740.png]]

- ### Lambda

	- Serverless Code .
	- Runs On-demand.
	- **Event Driven**
		- functions get invoked by AWS when needed
		- Can run multiple languages
		- We can also run Containers but ECS/Fargate is better.

	- Server - less CRON job.

	- Pay Per calls
	- Pay per Duration


- ### Amazon API Gateway 

	- To Expose the Lamda function, we need  an API Gateway
	-  ![[Pasted image 20260210213058.png]]
	- Serverless and Scalabe
	- Supports RESTful APIs and WebSocket APIs


### AWS Batch

- Fully Managed batch processing at any scale
- Efficiently run 100000s of computing batch jobs on AWS
- Batch service will dynamically launch EC2 instances or Spot instances
- Batch will provision right amount of compute.
- Batch jobs are defined as Docker Images and run on ECS.
- ![[Pasted image 20260210213453.png]]
- Batch VS Lambda

	- Lambda
		- Time Limit
		- Limited runtimes
		- Limited temporary disk space
		- Serverless

	- Batch
		- No time limit
		- Any runtime as long as its packaged as a Docker image
		-  Reply on EBS / Instance store for disk space


### Amazon Lightsail

- Virtual servers, storage, databases and networking
- Low and predictable pricing
- Simpler alternative to using EC2, RDS, etc
-  Great for people with little cloud experience


### CloudFormation

- A template type of way to Outline / create your AWS Infrastructure 
-  For Example , within a CloudFormation template we can ask for
	- Need a security group
	- Need 2 EC2 instances
	- Need a S3 bucket
	- Need a Load Balancer
- CloudFormation will create these for you in the right order , with the exact configuration that you specify
- Benefits
	- Infrastructure as a Code
		- No Resource created Manually
		- Changes to the infrastructure are reviewed through code.
	- Cost
		- You can estimate the cost of your resources using the CloudFormation Template
	- Can create Diagram for your template.
- Use Case
	- If you want to repeat an Infrastructure setup in different enviroments
	- Since it is a code , you can automatically setup an environment,


### AWS Cloud Development Kit ( CDK )

- Defining cloud Infrastructure using other languages:
	- Js, Python, Java, etc
- The code is compiled into a CloudFormation template (JSON/YAML)


### AWS Beanstalk

- Is a Platform as a Service
- Managed Service -> Beanstalk Manages
	- Instance configuration / OS is handled by Beanstalk
	- Deployment strategy
	- Capacity provisioning
	- Load balancing and auto scaling
	- Application health monitoring


### AWS CodeDeploy

- If we want to deploy our application automatically.
- Works with EC2 instance
- Can work with On-premise Servers as well.
- Hybrid Service


### AWS CodeCommit

- Similar to GitHub
- Amazons version of GitHub


### AWS CodeBuild

- Compiles source code, run tests and produces packages that are ready to be deployed
- ![[Pasted image 20260306195620.png]]
- Advantages
	- Fully Managed and serverless
	- Continuously scalable and highly available 
	- Pay as you go Pricing -> Paying for the time you code is being Build


### AWS CodePipeline

- Orchestrate the different steps to have code automatically pushed to production
- ![[Pasted image 20260306195917.png]]
- Basics of CI/CD
- ![[Pasted image 20260306200035.png]]


### AWS CodeArtifact

- Software packages depend on each other to be built
- 