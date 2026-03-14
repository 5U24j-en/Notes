		Reference - stephane maarek aws Course
### Cloud Deployment Models

- On Premise
- Cloud
- Hybrid

### Types of Cloud Computing

- #### IaaS - Infrastructure as a Service
	- Providing infrastructure like networking, computers, data storage space
	- Highest Level of flexibility
	-  Easy Parallel with traditional on-premise IT.
	- Eg.- EC2 
	
- #### Paas - Platform as a Service
	- Removes the need for your organization to manage the underlying infrastructure
	- Focus on the deployment and management of you application.
	- Eg - Elastic Beanstalk
	
- #### SaaS - Software as a Service
	- Complete product - software and infra managed by the service provider.
	- Eg. Rekognition for ML ( AWS )
	

![[Pasted image 20260121151318.png]]

## NOTE: Data Transfer IN is free. But Data Transfer out is Paid.

### AWS Regions

- Names start with `us-east-1, eu-west-3`
- A **Region** is cluster of **Data Centers**.
- How to choose an AWS Region
	- **Compliance** : Govt Data legal and compliance requirement
	- **Proximity**: Closer to the Customer
	- **Available services**: New features or services may not be available
	- **Pricing** : Pricing varies Region to Region


### AWS Availability Zones

- Each Region has Availability Zones 
-  **MAX - 6 , MIN - 3**
-  Sample - `ap-southeast-2a, ap-southeast-2b, ap-southeast-2c
-  Each ( AZ - Availability Zone) is one or more discrete Data Center with Redundant power, networking and connectivity
- ![[Pasted image 20260121152923.png]]
-  Separate from each other, isolated from disaster.

### AWS Point of Presence ( Edge Location)

- Includes AWS Regions and Caches , etc, 400+ locations

#### Shared Responsibility Model

![[Pasted image 20260121153517.png]]

Key Global Services

1. **AWS IAM (Identity and Access Management)**
2. **Amazon Route 53**
3. **Amazon CloudFront**
4. **AWS Global Accelerator**
5. **AWS Organizations**
6. **AWS WAF** (when used with CloudFront)
7. **AWS Shield**
8. **AWS Firewall Manager**


### IAM : Identity and Access Management (Global Service )

- **Root Account** created by default - complete Access
-  **Users** are people withing the organization ( can be grouped )
-  ![[Pasted image 20260121154916.png]]

- **IAM Policy**

	- User/Groups are assigned JSON Documents called Policies

	- ![[Pasted image 20260121155027.png]]
	- ![[Pasted image 20260305151424.png]]
	- ![[Pasted image 20260305151634.png]]
	
	
- ![[Pasted image 20260121155655.png]]
- **Inline policies** are Policies specifically for a single user.
-  IAM - Password Policy
	- You can set your own password policy like
		-  Set minimum password length
		-  Require Specific character type. Eg. lowercase, numer , etc
		-  Allow IAM users to change their own passwords
		- Password Expiration
		- Prevent Password Reuse
	- MFA - Multi Factor Authentication
		- 1 Factor Authentication
		- U2F Security Key. eg. Fingerprint Pen Drives

- IAM Roles -
	- Some AWS services will need to perform actions on your behalf
	-  Like an application on EC2 instance.
	- Example: 
		- EC2 instance Roles
		-  Lambda Function Roles
		- ![[Pasted image 20260129195134.png]]

	 NOTE: 
		 -  **IAM Groups** - are for USER access to services
		 -  **IAM Roles** - **IAM Roles are for any entity that needs temporary permissions**, including:
			- AWS services → EC2 → S3
			- Cross-account access
			- Federated users (SSO, Azure AD, Okta)
			- Lambda → DynamoDB
			- EKS pods → AWS APIs


- **IAM Security Tools**

	- IAM Credentials Report:
		- A report that lists all your accounts users and the status of their Credentials

	- IAM Access Advisor ( user level ):
		- Access advisor shows the service permissions granted to the user
		- You can see a usage report of the user.

| Security Tools           | Main Job                      |
| ------------------------ | ----------------------------- |
| **Access Advisor** *     | Find unused permissions       |
| **Access Analyzer**      | Find external access          |
| **Credential Report**  * | Audit user credentials        |
| **Policy Simulator**     | Test permissions              |
| **Trusted Advisor**      | Best practice security checks |
| **CloudTrail**           | Logs IAM activity             |



#### Shared Responsibility Model for IAM

- What is AWS Responsible for and What is a User Responsible for?
- ![[Pasted image 20260129200432.png]]

### Accessing AWS:

- AWS Management Console 
	- ( Web UI )
-  AWS Command Line Interface
	- ( Command Line application protected by access keys)
	-  To access AWS API we need to use the Access Keys
-  AWS SDK - 
	- When Applications want to Access AWS APIs , protected by Access Keys.

### AWS Compute Service:

- ##### Amazon EC2

	- EC2 -> Elastic Compute Instance
	-  Infrastructure as a Service
	-  Features
		- Renting Virtual Machines ( EC2 )
		- Storing Data on Virtual Drives ( EBS )
		-  Distributing Load across machines (Load Balancer) ( ELB )
		-  Scaling the services using an Auto Scaling Group ( ASG )


- ##### EC2 sizing and configuration Options

	-  OS = Linux, Windows, MacOS
	-  Compute = CPU / Cores
	-  Memory = RAM
	-  Storage 
		- Network Attached ( EBS & EFS )
		- hardware ( EC2 Instance Store )
	- Network Card:
		- Speed of the card
		- Public IP address 
	- Firewall Rules: Security Group
	-  Bootstrap Script ( Configure at Launch)


- ##### Bootstrap Script:
	- also known as EC2 User Data Script
	-  Bootstrapping - Launching commands when a machine starts
	-  Scirpt is runt only at the First start of an EC2 instance
	-  EC2 User Data used to automate boot tasks like
		-  Installing Updates
		-  Installing Software
		-  Downloading common files 
	- Runs with the Root User

Sample EC2 User Data Script

```
#!/bin/bash  
# Use this for your user data ( script from top to bottom)  
# install httpd (Linux 2 version)  
yum update -y  
yum install -y httpd  
systemctl start httpd  
system enable httpd  
echo "<h1>Hello World from $(hostname -f)</h1>" > /var/www/html/index.html
```

- **yum** 
	- **Linux package management command** used on RHEL, CentOS, and Amazon Linux.
	
- **"yum install -y httpd "**
	-  Installs the Apache **httpd** Web Server

- **`echo "<h1>Hello World form S(hostname -f)</h1>" >/var/www/html/index.html `**
	- Creates a HTML File with name index.html

- **>/var/www/html/**
	- This is httpd - default web root Directory / Folder


- ##### Amazon EC2 types 

	![[Pasted image 20260129214728.png]]
	- 5 -> Depends on the generation of Hardware

	-  **General Purpose** 
	
		-  Great for diversity of workloads
		-  Example. Web server , code repositories
		-  Balance between
			- Compute
			- Memory
			- Networking
		- Naming -  `t2.micro`

	-  **Compute Optimized**

		-  Compute Intensive Tasks which require high performance Processors.
			- Batch Processing Workloads
			- Media Trans coding
			- High Performance web servers
			-  ML/Scientific Modelling
			-  HPC ( High performance computing)
			-  Dedicated Gaming Servers
		- Naming  - `C6g`
	
	-  **Memory Optimized**

		- Fast performance for workloads that process large data sets in memory
		- Use Cases:
			- High performance, relational/non-relational databases
			- Distributed web scale cache stores
			-  In- memory data optimized for Business Intelligence ( BI )
			-  Applications performing real - time processing of big unstructured data
		- Naming - `R6g`

	-  **Storage Optimized**

		- Great for storage - intensive tasks that require high sequential read and write access to large data sets on local storage
		-  Use Cases:
			- High Frequency OLTP systems
			- Relational and NoSQL DBs
			-  Cache for in memory databases ( ex Redis )
			-  Data warehousing applications
			- Distributed File System


- ##### EC2 Security Groups 

	-  They control how (network) traffic is allowed into or out of EC2 instances.
	-  Security groups only contain **allow Rules**.
	-  Security groups rules can reference by IP or security group
	- ![[Pasted image 20260129220357.png]]
	- Security groups are like **Firewall** on EC2 instance.
	- They Regulate:
		- Access to Ports
		- Authorized IP ranges - IPv4, IPv6
		- Control inbound and outbound traffic
	- ![[Pasted image 20260129220514.png]]
	- ![[Pasted image 20260129220634.png]]
	- An instance can have multiple security groups
	- One Security group for a region/VPC combination
	- Security Groups are Outside EC2 instance ( So the **blocked** traffic wont be visible in application logs )
	- Debugging 
		- If `timeout ERROR ` -  could be Security Group issue
		- If `connection refused` - Could be application error, etc
	- ![[Pasted image 20260202190755.png]]
	- ![[Pasted image 20260202190943.png]]
	- ![[Pasted image 20260202191052.png]]



- ##### SSH Summary Table

	- ![[Pasted image 20260202192258.png]]
	
	- ##### Logging into AWS Instance with SSH - Linux
	
		- For Windows we need to use Putty
		-  ![[Screenshot from 2026-02-02 19-57-07.png]]
		- First we need to Use the Key Value pair that was downloaded while creating EC2 instance. Security Key - `EC2_First.pem`
		- `chmod 0400 EC2_First.pem`
			- -> We change access of the key to Read only for the owner of the system. No one else can access.
		- `ssh -i EC2_First.pem ec2-user@13.60.35.199` 
			- IP address is of the EC2 instance.

	- ##### EC2 Instance connect - Web Terminal

		- ![[Pasted image 20260202201314.png]]
		- ![[Pasted image 20260202201338.png]]

- #### EC2 Instance Purchasing Options

	- On Demand
		-  Short Workload and un-interrupted Workloads
		-  Pay by Second
		-  Highest cost but no Upfront payment

	- Reserved ( 1 & 3 Years )
		-  Long Workloads
		-  Highly discounted compared to On-demand
		-  You reserve instance attribute - Type, Region, OS, Tenacy
		-  1 Years and 3 Years
		-  Payment - No Upfront, Partial Upfront , All Upfront
		-  Scope - Regional or Zonal
		-  You can buy and sell in  Reserved Instance Marketplace

	- Convertible Reserved Instance
		- Long workloads with Flexible Instances
		-  Flexible scope, OS, tenancy
		-  Less discount compared to Reserved

	- Savings Plans ( 1 & 3 Years )
		- Commitment to an amount of usage
		-  Long Workload
		-  Commit to a certain Usage like 10$/hr or 3 years
		-  Locked to a Instance family and AWS region
		-  Flexible
			- Instance Size
			- OS
			- Tenancy

	- Spot Instance
		- The best Discount
		- Short Workloads
		- Cheap
		-  If the usage crosses the price you pay you can lose the instance.
		-  Useful for Workloads that can remain functional even if some components fails
			- Batch Jobs
			- Data Analysis
			-  Image processing
			-  Distributed Workloads
		- Not FOR 
			- DB
			-  Critical JOBS
			

	- Dedicated Hosts
		-  Book an entire physical server
		-  Control instance placements
		- Purchasing Options
			-  On - Demand
			-  Reserved
		- Most expensive Option
		- For strong Regulatory or Compliance 
		- Complex Software licensing

	- Dedicated Instance
		- No other customer will share your hardware
		- ![[Pasted image 20260203162339.png]]

	-  Capacity Reservation
		- Reserve capacity in a specific AZ for any duration

	- ![[Pasted image 20260203162643.png]]\

- #### Shared Responsibility Model - EC2

	-  ![[Pasted image 20260203162928.png]]



### EBS ( Elastic Block Storage)

- ##### EBS
	- An EBS Volume is a **Network Drive** you can attach to your instances while they run.
	- They are bound to Specific Availability Zone
		- To move , you need to snapshot it.
	-  It can be detached from an EC2 instance and attached to another one quickly
	- Have to provision capacity ( GBs, IOPS)
	- ![[Pasted image 20260203170946.png]]

	- **Delete On Termination attribute**
		- ![[Pasted image 20260204155546.png]]
		-  By default Root volume will be deleted when the EC2 instance is deleted
		- If you create a Volume and attach, by default delete on termination will be No.

	- ###### EBS Snapshot
		- Taking a Backup of the EBS Volume at a point of time.
		- Recommended to detach the Volume
		- Can copy snapshot across AZ or Region
		- ![[Pasted image 20260204160326.png]]
		- EBS Snapshot Tier
			- We can move to **Archive Tier** , which is Cheaper
			-  Takes 24 to 72 hrs for restoring the archive
		- Recycle Bin for EBS snapshots
			- Retention - 1 day to 1 year


### AMI - Amazon Machine Image

- AMI are customization of an EC2 Instance
	- You can add your own software , config , OS, monitoring
	- Faster boot/config since all software is pre packaged
- Example:
	- You create an EC2 instance and using User data script install Apache web server.
	- ```
	    #!/bin/bash  
		# Use this for your user data ( script from top to bottom)  
		# install httpd (Linux 2 version)  
		yum update -y  
		yum install -y httpd  
		systemctl start httpd  
		system enable httpd    
	  ```
	- ![[Pasted image 20260204162206.png]] 
	- Then we can create a AMI of a of the EC2 instance
	-  We can then launch a New instance with the AMI 
	- ![[Pasted image 20260204161933.png]]
	- And then in the User Data we only need to add the content or changes
	- ` echo "<h1>Hello World from $(hostname -f)</h1>" > /var/www/html/index.html `
	-  Instance Output
	- ![[Pasted image 20260204162247.png]]

### EC2 Image Builder

- Used to Automate the creation of Virtual Machines or Container Images
- Automate
	- -> Creation -> Maintenance -> Validation -> Testing  **OF EC2 AMI** 
- ![[Pasted image 20260204163431.png]]

### EC2 Instance Store

- EBS have limited Performance
- High Performance Storage - EC2 Instance Store
- EC2 Instance store lose their storage if they are stopped
- Good For
	- Buffer
	- Cache
	- Scratch Data
	- Temporary Content

### Elastic File System

- Managed - Network File System - EFS
- Can be mounted to 100s of EC2 Instance
- EFS only work with Linux EC2 in Multi AZ
- Features
	- Highly Available
	- Expensive
	- Pay per Use
	- No Capacity Planing
- ![[Pasted image 20260204164406.png]]

- **EBS vs EFS**
- ![[Pasted image 20260204164513.png]]

- **Storage Class - EFS IA**
	- Storage class is cost - optimized space for files not accessed every day
	- Very Cheap ( ex. 92 % )
	-  This is know as **EFS - IA**
	- EFS will autmatically move files to EFS-IA based on the **Life-cycle policy** the user creates
	- Example - Policy -> if files were not accessed in last 90 days move it to EFS-IA
	- ![[Pasted image 20260204165037.png]]


#### Shared Responsibility Model - Instance Storage

![[Pasted image 20260204165253.png]]

### Amazon FSx

- Used for launching third Party High Performance File System
- Fully Managed Service

- FSx for Lustre
	- For HPC ( High Performance Computing )
	- Use case -
		- ML
		- Analytics
		- Video Processing
		- Financial Modelling
	
	- ![[Pasted image 20260204170046.png]]
	
- FSx for Windows File Server
	- For Windows Instances
	- Built on Windows File Server
	- Supports SMB & Windows NTFS 
	- ![[Pasted image 20260204165908.png]]




