Databases and Storage - we are giving our coffee drinkers points on their cards now. 

You can think of block-level storage as a place to store files. A file being a series of bytes that are stored in blocks on disc. When a file is updated, the whole series of blocks aren't all overwritten. Instead, it updates just the pieces that change. This makes it an efficient storage type when working with applications like databases, enterprise software, or file systems.

When you use your laptop or personal computer, you are accessing block-level storage. All block-level storage is, in this case, is your hard drive. EC2 instances have hard drives as well. And there are a few different types.

When you launch an EC2 instance, depending on the type of the EC2 instance you launched, it might provide you with local storage called instance store volumes. These volumes are physically attached to the host, your EC2 instances running on top of. And you can write to it just like a normal hard drive. The catch here is that since this volume is attached to the underlying physical host, if you stop or terminate your EC2 instance, all data written to the instance store volume will be deleted. The reason for this, is that if you start your instance from a stop state, it's likely that EC2 instance will start up on another host. A host where that volume does not exist. Remember EC2 instances are virtual machines, and therefore the underlying host can change between stopping and starting an instance.
Local storage - instance store volumes - however, our EC2 might start again on another host. Therefore we will just lose that memory. 

Because of this ephemeral or temporary nature of instance store volumes, they are useful in situations where you can lose the data being written to the drive. Such as temporary files, scratch data, and data that can be easily recreated without consequence.

With EBS, you can create virtual hard drives, that we call EBS volumes, that you can attach to your EC2 instances. These are separate drives from the local instance store volumes, and they aren't tied directly to the host that you're EC2 is running on. This means, that the data that you write to an EBS volume can persist between stops and starts of an EC2 instance.

Since the use case for EBS volumes is to have a hard drive that is persistent, that your applications can write to, it's probably important that you back that data up. EBS allows you to take incremental backups of your data called snapshots. It's very important that you take regular snapshots of your EBS volumes This way, if a drive ever becomes corrupted, you haven't lost your data. And you can restore that data from a snapshot.

## Instance Stores
Block level storage - data in fixed-sized blocks. Blocks have unique addresses, accessed independently, highly efficient for databases, virtual machines, and high performance applications. 
SAN - Storage Area Network. 
An [**instance store**(opens in a new tab)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/InstanceStorage.html) provides temporary block-level storage for an Amazon EC2 instance. An instance store is disk storage that is physically attached to the host computer for an EC2 instance, and therefore has the same lifespan as the instance. When the instance is terminated, you lose any data in the instance store.

![[Pasted image 20250130141438.png]]

![[Pasted image 20250130141442.png]]![[Pasted image 20250130141449.png]]
Data here is volatile, it will just get deleted when we turn the instance off. 

[**Amazon Elastic Block Store (Amazon EBS)**(opens in a new tab)](https://aws.amazon.com/ebs) is a service that provides block-level storage volumes that you can use with Amazon EC2 instances. If you stop or terminate an Amazon EC2 instance, all the data on the attached EBS volume remains available.

Because EBS volumes are for data that needs to persist, it’s important to back up the data. You can take incremental backups of EBS volumes by creating Amazon EBS snapshots.

Snapshots for EBS backup. 

## Amazon EBS Snapshots
![[Pasted image 20250130141615.png]]
Incremental Backup. 
We just add what has changed, the first, all the data in, then only the changes after that, just like Git. 

Incremental backups are different from full backups, in which all the data in a storage volume copies each time a backup occurs. The full backup includes data that has not changed since the most recent backup.

## Amazon Simple Storage Service
![[Pasted image 20250130141850.png]]
The data might be an image, video, text document, or any other type of file. Metadata contains information about what the data is, how it is used, the object size, and so on. An object’s key is its unique identifier.
Recall that when you modify a file in block storage, only the pieces that are changed are updated. When a file in object storage is modified, the entire object is updated.
[**Amazon Simple Storage Service (Amazon S3)**(opens in a new tab)](https://aws.amazon.com/s3/) is a service that provides object-level storage. Amazon S3 stores data as objects in buckets.
Objects in buckets. 

You can upload any type of file to Amazon S3, such as images, videos, text files, and so on. For example, you might use Amazon S3 to store backup files, media files for a website, or archived documents. Amazon S3 offers unlimited storage space. The maximum file size for an object in Amazon S3 is 5 TB.

5 TB wow.

When you upload a file to Amazon S3, you can set permissions to control visibility and access to it. You can also use the Amazon S3 versioning feature to track changes to your objects over time.

## Amazon S3 Storage Classes

- How often you plan to retrieve your data
- How available you need your data to be

**S3 Standard**
- Designed for frequently accessed data
- Stores data in a minimum of three Availability Zones

Amazon S3 Standard provides high availability for objects. This makes it a good choice for a wide range of use cases, such as websites, content distribution, and data analytics. Amazon S3 Standard has a higher cost than other storage classes intended for infrequently accessed data and archival storage.

**S3 Standard - Infrequent Access**
- Ideal for infrequently accessed data
- Similar to Amazon S3 Standard but has a lower storage price and higher retrieval price

Amazon S3 Standard-IA is ideal for data infrequently accessed but requires high availability when needed. Both Amazon S3 Standard and Amazon S3 Standard-IA store data in a minimum of three Availability Zones. Amazon S3 Standard-IA provides the same level of availability as Amazon S3 Standard but with a lower storage price and a higher retrieval price.
Low storage - high retrieval

**S3 One Zone - Infrequent Access**
Usually 3 zones - this one - one zone. 
- Stores data in a single Availability Zone
- Has a lower storage price than Amazon S3 Standard-IA

Compared to S3 Standard and S3 Standard-IA, which store data in a minimum of three Availability Zones, S3 One Zone-IA stores data in a single Availability Zone. This makes it a good storage class to consider if the following conditions apply:

- You want to save costs on storage.
- You can easily reproduce your data in the event of an Availability Zone failure.

**S3 Intelligent-Tiering**
Changing Access Patterns. 
- Ideal for data with unknown or changing access patterns
- Requires a small monthly monitoring and automation fee per object

In the S3 Intelligent-Tiering storage class, Amazon S3 monitors objects’ access patterns. If you haven’t accessed an object for 30 consecutive days, Amazon S3 automatically moves it to the infrequent access tier, S3 Standard-IA. If you access an object in the infrequent access tier, Amazon S3 automatically moves it to the frequent access tier, S3 Standard.

**S3 Glacier Instant Retrieval**
- Works well for archived data that requires immediate access
    
- Can retrieve objects within a few milliseconds
    

When you decide between the options for archival storage, consider how quickly you must retrieve the archived objects. You can retrieve objects stored in the S3 Glacier Instant Retrieval storage class within milliseconds, with the same performance as S3 Standard.

**S3 Glacier Flexible Retrieval**
Glacier - archive. 
- Low-cost storage designed for data archiving
- Able to retrieve objects within a few minutes to hours

S3 Glacier Flexible Retrieval is a low-cost storage class that is ideal for data archiving. For example, you might use this storage class to store archived customer records or older photos and video files. You can retrieve your data from S3 Glacier Flexible Retrieval from 1 minute to 12 hours.

**S3 Glacier Deep Archive**
- Lowest-cost object storage class ideal for archiving
- Able to retrieve objects within 12 hours

S3 Deep Archive supports long-term retention and digital preservation for data that might be accessed once or twice in a year. This storage class is the lowest-cost storage in the AWS Cloud, with data retrieval from 12 to 48 hours. All objects from this storage class are replicated and stored across at least three geographically dispersed Availability Zones.
Super slow, super cheap. 

**S3 Outposts**
- Creates S3 buckets on Amazon S3 Outposts
    
- Makes it easier to retrieve, store, and access data on AWS Outposts
    

Amazon S3 Outposts delivers object storage to your on-premises AWS Outposts environment. Amazon S3 Outposts is designed to store data durably and redundantly across multiple devices and servers on your Outposts. It works well for workloads with local data residency requirements that must satisfy demanding performance needs by keeping data close to on-premises applications.

![[Pasted image 20250130143342.png]]![[Pasted image 20250130143613.png]]
What is the use case? 

S3 web enabled - regionally distributed (already the backup strategy) - cost savings - serverless

Object storage - Block Storage

Object - file as an object (discrete) - great for images - videos - any time we change it, we have to upload the entire file - there are no delta updates. S3 for only occasional changes.

Block storage - only update the necessary part - not the whole - like our HDD's on our computers - great for video editing. EBS is our guy for lots of changes. 

Each is the right service for what we need. 

## Amazon Elastic File System
Shared file system - like on-premises - keep up with data - backups - access. 
Multiple instances can access EFS - auto scaling. 
EBS - also does this - but the difference is : attach to E2 - availability zone level resource - no scaling - need to be in the same AZ as EC2. 

EFS - multiple instances reading and writing simultaneously - linux file system - regional resource that automatically scales. 

**File Storage**
File storage - multiple clients (users, apps, servers) can access in the shared file folders. 
Block storage for this, with local (linux) file system to organize the files. Then access data through file paths. 
Good for same time access from many clients. 

EFS (elastic) therefore scalable - can be on-premises as well - grow/shrink automatically. Scale to petabytes without disrupting applications. 

EBS and EFS

EBS - single AZ - attaches to an EC2 - EC2 and EBS in the same zone

EFS - Multi regional, across many AZ's. Then duplicated data, accessible by many AZ's. 
On-premises servers can use EFS using AWS Direct Connect. 

## Amazon Relational Database Service RDS
Storing data in various system - but we want to maintain relationships between data - say someone orders the same thing, then we might offer them a promotion. 
Storing data in a way that relates to other pieces of data. 
Queries in SQL - say someone in a customer table - then they have an address in another, there needs to be a relationship there. 
Remember SQL in A levels. 

![[Pasted image 20250130145143.png]]
On premises usually runs one of those. 
Lift and shift migration - putting on-premises over to the cloud with the same relationships. 

This is a more managed service - automated patching - backups - redundancy - failover - disaster recovery. I don't even need to think about this. 
Focus on business problems, and not work on just this alone. 

Amazon Aurora - 1/10th the cost - MySQL - PostgreSQL - data replication - 15 read replicas 
offload reads and scale - continuous backups to S3 - point in time recovery - specific period recovery. 

## Relational DB's
![[Pasted image 20250130145511.png]]
Amazon RDS is a managed service that automates tasks such as hardware provisioning, database setup, patching, and backups. With these capabilities, you can spend less time completing administrative tasks and more time using data to innovate your applications. You can integrate Amazon RDS with other services to fulfill your business and operational needs, such as using AWS Lambda to query your database from a serverless application.

Amazon RDS provides a number of different security options. Many Amazon RDS database engines offer encryption at rest (protecting data while it is stored) and encryption in transit (protecting data while it is being sent and received).

Amazon RDS is available on six database engines, which optimize for memory, performance, or input/output (I/O). Supported database engines include:

- Amazon Aurora
- PostgreSQL
- MySQL
- MariaDB
- Oracle Database
- Microsoft SQL Server

## Amazon Aurora
[**Amazon Aurora**(opens in a new tab)](https://aws.amazon.com/rds/aurora/) is an enterprise-class relational database. It is compatible with MySQL and PostgreSQL relational databases. It is up to five times faster than standard MySQL databases and up to three times faster than standard PostgreSQL databases.

Amazon Aurora helps to reduce your database costs by reducing unnecessary input/output (I/O) operations, while ensuring that your database resources remain reliable and available. 

Consider Amazon Aurora if your workloads require high availability. It replicates six copies of your data across three Availability Zones and continuously backs up your data to Amazon S3.

## DynamoDB
Database - serverless - create tables - table store and query data - items - attributes - different features of data - 1 - 2 mil DDB will auto scale - sources data redundantly - mirrors across drives - massively scalable - 1 ms response time - super responsive - relational require schema (many tables) that relate to each other, sclaing performance issues (no variation due to rigid schema) - a less rigid dataset with high access rate we need non-relational - simple flexible schemas - add and remove attributes in the table (flexible tables) that dont' need the same attributes per item - can't use SQL - attributes are keys - only focusses on one table - therefore fast response
No SQL non relational 
specific use cases 
1ms response time 
fully managed - serverless 
highly scalable

Prime day 4.71 trillion access to DynamoDB 

## Non-Relational DB
Create tables - store and query data there. 

NoSQL - often caslled that - use structures other than rows  and columns to organize. 
Key Value pairs is one way. 
Data in items (keys), have attributes (values). 
Attributes - different features of the data. 

Also not every item has to have the same attributes: 
![[Pasted image 20250130150336.png]]
## Amazon DynamoDB 
Key value dbs. 
Deliver in single-digit ms performance at any scale - that is nuts (that is some hashing right there). 
Serverless - no provisioning, patch or management on our end. No need to install, maintain and operate software. 
Auto-scaling - think about prime day - auto scaling

VS 
Relational - complex analysis of data, across multiple tables, strength of any relational system. Built for analytics, due to the complex relational joins. 

Most time we use relational - we don't actually need it - DynamoDB is there for you - 

## Amazon Redshift
Data analysis - Velocity of real time read write, volume of data will overwhelmed even the beefiest of databases - and the variety is an issue - a single query over multiple DBs is not easily handled - data warehouses big data (historical analytics) where the data is set, it won't change - looking backwards at our data. 
Data warehouse tuned - 
Redshift - data warehousing as a service - nodes in petabyte sizes - a single query in xetabytes - better performance than normal DBs - much faster apparently


## AWS Database Migration Service DMS
On premises - how do we move it to AWS - migrate existing in a secure and easy fashion. 
Source -> target DB 
Source will remain operational during this transition - they dont have to be of the same type - no down-time

Homogenous - same type DBs - SQL to Amazon RDS for SQL etc. etc. 

The schema structures and data types and code are the same. 

Heterogenous - source and target do differ in type - Most of the above will be different - AWS schema conversion tool - schema and code will change - then use the system to migrate them over 

Development and test DB migrations 
DB consolidation 
and Continuous replication

Copy DB to a dev or test environment. 
Consolidation - aggregate a bunch to one 
Continuous data replication - disaster recovery - or geographic separation. 


Enable devs to test against a copy of production data without affecting users 

Combine several DBs into one single one

Continuous sending of copies to other sources. 

## Additional
Choosing the right DB - right storage platform - there is no one-size-fits all. 
Amazon DocumentDB Document database server

Amazon Neptune - graph database - 

Amazon managed block chain. 

Amazon Quantum Ledger Database (QLDB) - immutable - any entry can never be removed. 
Review complete history of all the changes that have been made to your application data. 

Database Accelerators - Caching layers - read times of common requests 

Amazon ElastiCache
