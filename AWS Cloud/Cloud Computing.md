The model that AWS are going for is the pay as you go, so that you don't have to pay for something that you don't use, then the benefit of having this cloud model is that we have everything on demand. If we suddenly need 20 terabytes of storage, then we just pay for it, and we have it right there. We don't have to wait, and we don't have to do it ourselves as a company, we just pay for as much as we use. 

#### What is Cloud Computing
The on-demand delivery of IT resources over the internet, with pay-as-you-go pricing. 
AWS has the resources that most businesses will need, at any time. 
If you just suddenly need 300 virtual servers, in just a few clicks, you can have them. 
Then at a sudden, you just return them and stop paying for them. 
This is just not possible if we were to do it on our own. 

Why so many products: BECAUSE A BUSINESS WILL THEM. 

The way that we build the data in these resources is what really matters. 

Undifferentiated (not divided or not able to be divided) heaving lifting of IT. 
Tasks that are common, often repetitive and ultimately time consuming, that is what AWS wants to help with. 
Therefore, a business can build on what makes them unique and not the boring stuff. 

Then, over the internet, we can access these items.

The pricing means that we aren't paying for 100% of the "might be used" stuff, when we don't need, we just pay for as much as we are going to use. Like, in an office, if you pay for a whole floor, then all of a sudden, only two of three employees turn up, you are missing out on so many chairs that could be filled, wasted money, therefore, we should only pay for the ones that we use, AWS allows for that. 


##### Infrastructure 
Infrastructure (IT) means the foundational systems and resources needed to run and support tech services and apps: 
1. Hardware : servers, storage devices, and networking equipment
2. Software : OS's, and tools for managing that hardware resources
3. Networks : Internet, intranet, and other comms systems. 
4. Facilities : Data centres or cloud environments where hardware is housed
The essential framework, allowing everything else to function properly.
#### Deployment Models for Cloud Computing
When selecting a strategy, there are factors to be considered: required cloud application components, preferred resource management tools, and legacy IT infrastructure requirements (the old stuff). 

There are: 
- **Cloud-Based**
- **On-Premises**
- **Hybrid**
##### Cloud-Based - Run all parts of the app in the cloud
- Migrate existing apps to the cloud
- Design and build new apps on the cloud too
Everything on the cloud. 

**Low Level Infrastructure:** Your IT staff manages most of the components, such as servers, storage, networks and OS's. 
High level of control over the infrastructure, that requires greater expertise to configure, manage, and maintain.
Traditional on-premises servers or Infrastructure-as-a-Service (IaaS) platforms like AWS or EC2.. 
Increase management burden, requiring manual scaling and handling of operational tasks like patching and backing up. 
More control, however, a team has to work on it all the time, and manage every detail, more prone to error. 

**High Level Infrastructure**
Referring to **managed services or platforms** that abstract away much of the complexity of infrastructure management. Scaling, patching and availability are often automated, and don't require a team to work on specifically. 
AWS lambda (serverless) - AWS Elastic Beanstalk
Easier to develop and deploy applications. Frees up IT staff to focus on innovation rather than infrastructure management.
Less control, easier to set up. 
##### On-Premises

Hosting and managing IT infrastructure, apps, and resources within an **organization's own data centre or physical location**, rather than relying on third party cloud services.

They can incorporate **virtualization technologies** to create a **private cloud**, enabling better utilization and flexibility.
Using tools like VMWare or Hyper-V to create virtual machines (VMs) for efficient server use. 

Then we use tools in order to monitor and allocate resources (CPU, Memory, and storage). 

**Virtualization Technologies** : allows us to create multiple virtual versions of a resource (like servers, storage, or networks) on a single physical system. Reducing costs, improving resource utilization, reduces costs, and makes infrastructure more flexible and scalable. 
1. Hypervisor - Software to create VMs (virtual machines) (bare-metal - running directly on hardware for better performance, or hosted - runs on an OS)
2. Virtual Machines - Independent instances with their own OS and apps, running on the same hardware. Each VM is isolated, allowing for multiple environments to run on a single server. 
3. Storage Virtualization - (SAN Storage Area Network) Combining storage devices into one manageable virtual storage pool. 
4. Network Virtualization - Abstracting and combining network resources into software-defined network (SDNs). (VLANs - virtual LANs). 
5. Containerization - A lighter form of virtualization where applications run in isolated user spaces (containers) but share the host OS. Docker, or Kubernetes. 
The benefits are that we have better resource utilization : As in we reduce hardware idling on a single machine.
There is a reduction in physical hardware needed, the more we can fit on one system, the fewer systems we will need. 
Scalability is easier, if we can just make more VMs (which we can). 
VMs are super flexible, they can be configured, made and destroyed quickly. 
Virtualizations create easy backups, and migrations, improving recovery times. 

###### **Use Case Example**:

- A single server running VMware ESXi could host:
    - A web server on one VM.
    - A database server on another VM.
    - A development environment on a third VM.

Each VM operates independently, even though they share the same physical server.

##### Hybrid Development
- Connect cloud-based resources to on-premises infrastructure. 
- Integrate cloud-based resources with legacy IT applications. 

Cloud-based resources are connected to on-premises infrastructure. 
There are a few reasons, there could be legacy apps that are better maintained on premises, or government regs require your business to keep certain records on the premises. 
Imagine that we use the cloud for batch data processing, however, there are legacy apps that need to be in house/on-premises. 

### What is a Cloud: 
A series/network of servers, storage, and other resources accessed over the internet. 
On-demand computing, like data storage, applications, and processing power without requiring local hardware. 
- **Public Cloud**: Shared infrastructure managed by third-party providers (e.g., AWS, Azure, Google Cloud).
- **Private Cloud**: Dedicated infrastructure for one organization, either on-premises or hosted.
- **Hybrid Cloud**: Combination of public and private clouds.
The providers will manage maintenance and updates. 


#### Benefits of Cloud Computing
- Trade upfront expense for variable expense 
	- Upfront expense : buying the software - hardware etc. before actually using them. 
	- Variable expense : only paying for what we use, which will vary, based on usage. The tight cost on what we use is so much better than paying a huge amount, then we might not use all of what we have. 
- Stop spending money to run our own data centres : There is actually more time spent on maintaining and managing infrastructure and servers, rather than just using this. If we use cloud computing, then we just focus less on the maintenance etc, and more time on our applications and customers. 
- Stop guessing on Capacity - you don't have to predict how much infrastructure capacity you will need before deploying an application. We can launch AWS EC2's when needed, only pay for what we used, and the compute time etc. We can scale in and scale out in response to demand. 
- Benefit from massive economies of scale - achieving lower variable cost than we would on our own. AWS can achieve higher economies of scale (as the scale grows, the price per unit will typically drop for users). Bulk buying will be cheaper than buying one by one. Lower pay-as-you-go prices. 
- Increase speed and agility - The flexibility allows for easier development and deploy applications. We can just innovate, all the server stuff is done for us. 
- Going global in minutes - deploy apps to users all around the world in minutes. AWS is everywhere. The latency will be lower as well, as AWS IS EVERYWHERE. 



### Cloud Computing Models
### IaaS
Infrastructure-as-a-Service : Delivering infrastructure like compute, storage, and network resources on a pay-as-you-go basis over the internet. The provider is responsible for maintaining the hardware. 
Imagine as well, an e-commerce company; throughout the year they may have light-moderate traffic, however, if they were to build their own, they would have to be ready for the worst case scenario, meaning they would have to be ready for the massive rush during the holidays. They would have to have a huge infrastructure, that really won't be used, only for a short time. IaaS vendors will solve this. You get flexible access to secure, almost "unlimited" resources. 

**Speed**: gain a huge number of resources within minutes. You can focus on the core of your business, the rest will rely on the provider. Focus on the parts that make your company great, not on the data fumbling. 

**Performance**: geographically as well. Positions that will be closer to the customer as well. 

**Back-up and Recovery**: Providing fantastic systems for this. 

**Competitive Pricing**: Pay-as-you-go, only for what you have used. 

**How does it work?**: 
Working on the principle of visualization: An IaaS vendor lets you select the type of configuration of the infrastructure that you require. 
Then the system will create a digital version of the underlying infrastructure. The virtualizations will mimic the behaviour of the physical, without having to acquire it. 
For the user, everything will work the same as if it were the physical thing. 
You can also look at: 
- view system logs and monitor performance
- implement consistent security measures across all the infrastructure
- configure policies that automate common infrastructure tasks, like backup and load balancing. 

**Types of Services Provided:**
**Compute**: 
Providing CPUs, GPUs, RAM : and users can request in the form of virtual machines or cloud instances. Cloud services then provisions the required capacity, then we can run our planned tasks in that environment. 

**Storage**: Block Storage - storing data in blocks like SSD or Hard Drive. 
File Storage - stores data as files like in a NAS (network attached storage). 
Object Storage - stores data as objects similar to those in OOP. 

**Networking**: Including routers, switches, and load balancers. Again we virtualize the networking functions of these appliances in software. 

### iPaaS
Integration platform as a service (iPaaS) is a cloud based software model for integrating data from multiple applications into a single solution. 
Like a digital translator that helps different apps work together, the I is really important here, the integrating part of it. 
It will automate the flow of data between cloud-based and on-premises systems without requiring custom code.
Bridging gaps between different tools, like a CRM (e.g. Salesforce) to ERP system like (SAP). 
Will transform data formats between systems (eg. converting JSON to XML). 
Linking cloud with on-premises. Linking e-commerce platforms with inventory management systems. 
Reduces the need for us to write our own manual integration or custom scripts. 
Most businesses have an array of multiple applications, that handle various aspects of their business processes and IT operations. 
They will have to integrate all that data, for analytics and deeper business intelligence. 
With iPaaS, we can integrate data from third-party software as a service (say SaaS), and on-premises data centers, into a centralized cloud-based service. 
It will abstract underlying data integration complexities. 
They are the middleware technologies that we would otherwise have to create. 

**Automated Workflows**: automate between systems, streamlining operations and reducing manual processes. Real-time data sync between connected systems. Analytics will always be up to date. 

**Speed at Scale**: Prebuilt connectors and templates that support faster deployment of complex integrations at scale. Then we can integrate various systems regardless of where they are hosted - on-premises, private cloud, or with different cloud providers.

**Secured and Simplified Integration**: Reducing need for in-house integration specialists or bespoke solutions. There is no compromise around security and quality. You can also monitor all integrations from a centralized dashboard, streamlining error detection and incident management. 

**Data sync across applications**: ensure consistency across different data sources, performing real-time operations.

**Application Integration**: You could create records in Salesforce from new Marketo leads. You are essentially merging the business logic of multiple applications and how they work.

**Data Migration**: Transferring data from multiple sources. You might want to transition from on-premises hosting to cloud storage, upgrade legacy systems to cloud-based systems like an ERP, or integrate data sources for cost benefits. 

**Automation**: Process automation. 

**How does it work?**:
Share data across third-party SaaS applications. 
Typically provide a dashboard or user-friendly interface. 
You can configure your sources, and your targets as well. 

Data structures often vary between systems, therefore iPaaS will transform that data between the systems if we need to. 
Also, there can be triggers to do something, like a new record in an application triggers a specific integration action - eg. creating a new record in another application. 

### SaaS
**Software as a Service**: Delivering applications to end-users through an internet browser. 
Hosting services and apps for customers to access on-demand. 
Users have nothing to do with anything underlying the app itself, they only have to think about how they are going to use the app itself. 
Businesses can access powerful software that is super expensive to run. 
The vendor will manage the software, the hardware etc. 
Then we can access from anywhere.
Then the user will subscribe to that model. 

**Cloud Accessibility**: We can access the software from an internet connection. Otherwise we would need to run that app on a workstation. 

**Lower Upfront Costs**: Typically a subscription based model, reducing upfront costs of traditional software such as licenses, installation, or infrastructure management. And also actually running the software. 

**Rapid Deployment**: Eliminates installation and configuration associated with on-premises software, you can roll out software across your business as soon as your enterprise subscriptions begin. 

**On-demand Scalability**: Perfect for quick growing businesses. 

**Reliability**: security, and disaster recovery. Promising 99% and higher uptime, all we need is a reliable internet connection. 

**Automatic Updates**: imagine never needing to manually update something ever again. They can be set to deploy automatically. 

**Integration**: Using APIs we can integrate apps with other systems. Customizing software to suit your particular requirements without infrastructure costs. 

**Real-time Data and Analytics**: Real-time insights.

**How does it work?**: Vendors will host applications and data on their own servers and databases. They also vendor platform, operating systems, and middleware. 
Then the vendor will have to give access to the user through web browsers, in order to use the app. 
This means that multiple people can use the same service as well. 
Then you need a Service Legal Agreement : for uptime, security, support, and automatic updates, and outlining the responsibilities of the client. Most businesses need to own their data, no matter where it's held. 

- Customer Relationship Management ([CRM](https://aws.amazon.com/marketplace/b/98bce01b-8e07-4a83-8e4e-711ce14f2e88?category=98bce01b-8e07-4a83-8e4e-711ce14f2e88))
- Enterprise Resource Planning ([ERP](https://aws.amazon.com/marketplace/search?searchTerms=erp))
- Email marketing software
- Accounting software
- Human resources software
- Security software
- Collaboration tools
- Document editing services
- Communication software
- Contact Center software

Platform as a Service

Platform as a Service provides hardware and software infrastructure for constructing and maintaining applications typically through APIs. Cloud providers host hardware and software development tools in their data centers. With PaaS, you can build, test, run, and scale applications faster and at a lower cost.

[Learn more about Integration Platform as a Service (iPaaS)](https://aws.amazon.com/what-is/ipaas/)

#### _SaaS vs. PaaS_

Whereas SaaS is used to do specific tasks, PaaS gives you access to managed infrastructure for application development.
Platform as a Service

Platform as a Service provides hardware and software infrastructure for constructing and maintaining applications typically through APIs. Cloud providers host hardware and software development tools in their data centers. With PaaS, you can build, test, run, and scale applications faster and at a lower cost.

[Learn more about Integration Platform as a Service (iPaaS)](https://aws.amazon.com/what-is/ipaas/)

#### _SaaS vs. PaaS_

Whereas SaaS is used to do specific tasks, PaaS gives you access to managed infrastructure for application development.
![[Pasted image 20250125232712.png]]

![[Pasted image 20250126002413.png]]

### Additional Compute Services
EC2 virtual machines. 

#### Serverless
Meaning we have servers, but we need not do anything with them. 

AWS Lambda: 
#### AWS Lambda
Run code without needing to manage or provision servers. 
You pay for the compute time. 
Charge when running our code. 
We might have code that just resizes imagines automatically to the AWS cloud. 

![[Pasted image 20250129180251.png]]
Then the trigger will be the uploading of an image. 

## Containers
Standard way of packaging our app's code, with dependencies into a single object. 
There are containers for workflow as well, for users, and workers at a company. 
![[Pasted image 20250129180430.png]]Pre-configured, isolated environments. 
Consistent across other platforms. 

Portable, consistent, scalability. 

The issue is monitoring all of these./ 

### Amazon Elastic Container Service ECS
Will support docker containers. 

### Amazon Elastic Kubernetes Service EKS
Running Kubernetes on AWS: 
Kubernetes : open source platform to automate deployment, scaling, and management of containerized applications, like container orchestration, service discovery and load balancing, self healing, replacing and rescheduling containers that have failed. 
Automated rollouts and rollbacks. 

### AWS Fargate
Serverless compute engine for containers. 
Both with ECS and EKS. 
Will manage our sever infrastructure for us. 
