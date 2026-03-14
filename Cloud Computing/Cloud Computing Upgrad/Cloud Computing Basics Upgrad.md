
-  Cloud Advantages:
	-  Elasticity - Pay per use
	-  High Availability
	-  Global Reach
	-  Scalability
	
- Cloud Advantages for ETL/ Data Engineering
	-  Scalability
	-  Parallel Processing
	-  Integration
		-  Native support to data sources like:
			-  databases
			-  APIs
			-  Cloud storage
			-  Kafka		
	- Pay as you go


- Cloud Services Models:
	-  IAAS-> ( Infrastructure as a Service )
	- PAAS -> ( Platform as a Service )
		-  Provider manages the infrastructure OS and runtime environments
		-  You can run develop and configure applications
		-  Example: AWS Glue, Google Cloud Data-flow, etc
	- SAAS -> ( Software as a Service )

-  Cloud Deployment Models:
	-  Public Cloud
		- Example: AWS,  Google Cloud
	-  Private Cloud
		- Used by Single Organisation
		-  Infrastructure On premise
	-  Hybrid Cloud
	-  Multi Cloud Setup
		-  Using services from more than 1 services, like using both AWS and Azure together

- Cloud Infrastructure:
	-  Data Centers
		-  Physical Infrastructure
	-  Zones:
		-  Local grouping of Data Centers
			-  Example: asia-east1-a -=- AWS zone in Taiwan

- Building Blocks of Cloud Computing:
	-  Compute
		-  Processing power provided by the cloud
		-  Compute resources are billed based on time, usage and resource allocation (CPU/Memory)
		- Types of Machines/ nodes
			-  VM - Virtual Machines
				-  Software Based Emulation of a Physical computer
				-  Runs of Hardware but runs like a independent machine
				-  It has its own OS, config settings, etc
			-  Containers
				-  Lightweight compared to VM
				-  Isolated environments but share the Host Kernel
				-  Faster startup and shutdowns
				-  Ideal for Micro services 
			- Server-less Compute:
				-  Eliminates server management
				-  Upload code and Cloud providers manage the execution
		
	-  Network
		-  IP address, DNS ,  Routing, etc.
		-  VPC ( Virtual Private Cloud):
			-  Isolated private section of public cloud for ETL infrastructure
			-  Complete control over network structure, IP range, route tables
		-  Load Balancer:
			-  Distributes network traffic over multiple servers
			-  High performance and prevents bottlenecks
		- Firewalls and Security Groups
			-  Enforces network access control for ETL ecosystem.
		- Internet Gateway
			-  Enables resources in VPC to connect with the public internet securely
		- VPN
			-  Provides secure encrypted connection
	-  Storage
		-  Object Storage:
			-  For strong unstructured and semi structured data
			-  Like images, videos , backups, etc
			-  Example - Amazon S3, Azure Blob Storage
		- Block Storage:
			-  Works like traditional HDD or SSD.
			-  Fixed Datablocks
		-  File Storage:
			-  Provide shared access to files using Network Protocols

-  AWS
-  Google Cloud
-  Microsoft Azure 




#### VPC: 


![[Pasted image 20260120100203.png]]


Amazon Messaging Services:

-  SNS - Simple Notification Service
-  SQS - Simple Queue Service

Server-less Computing

AWS Lambda 
- Run code without provisioning or managing servers


AWS ECS
AWS Kubernetes EKS

Serverless Containers-AWS Fargate
AWS Cloud Front - CDN ( Content Delivery) - Edge Location

AWS Outpost - Private Cloud

NACL - Stateless
Security Group - Statefull

Amazon S3

S3 - Classses
- S3 standard
- S3 Standard - IA
-  S3 One Zone IA