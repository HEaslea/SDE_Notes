# And Reliability

Imagine a parade in front of our favourite coffee shop. 

The trick is to be highly available at all times, lol, but yes, that is it. 

We might have another shop, very close. 

It's not good enough to have one data centre. 
AWS operates in all sorts of areas, in order to work around the world. 
These areas are called Regions. 

## Regions
What do we do when disaster strikes our building (data centre). 
We could run a second one? 
Hope is not a good business plan. 

AWS built regions that would be closest to where the businesses are. 
Regions can be connected to each other Region, through a high speed fibre network. 

You need to think about security when we transfer data between these two. 

Eg. government compliance requirements that your financial info in Frankfurt, not leave Germany. 
With the right credentials and permissions, data may be able to leave that region. 

Which region? : There are four business factors that go into this. 

1. Compliance - Say our data has to stay in UK, then London. Or China. 
2. Proximity - How close our customer base will be. Consider games, latency must be low.
3. Feature Availability - Sometimes the closest region will not have all the features that we need. eg. Amazon Braket - the new quantum computing platform - then they might have to wait for the closest region to install that feature.
4. Pricing - Some locations are just more expensive. 

### Availability Zones
Each Region is made up of multiple data centres. 
AZ - >= one data centres, with redundant power, networking and connectivity. 
EC2's will launch on some hardware that is installed in an Availability Zone. 
Each AWS Region, made up of isolated and physically separate AZ's within a Geographic Region. 

Run multiple EC2 instances, don't run them in the same building. Not even on the same street. 
Push them apart until the speed of light is too slow. 

Then if disaster strikes, then only some of our instances will be knocked, not all. 

Always run across 2 AZ's. 

![[Pasted image 20250129184202.png]]AZ - single data centre, or a group of within a Region. 
They are close enough to have very low latency. Yet they are distant enough to make sure that if there is a disaster, then it won't knock them all out. 
![[Pasted image 20250129184358.png]]![[Pasted image 20250129184408.png]]
Best practice is to run across at least two AZ's. 
![[Pasted image 20250129184448.png]]
Then if one goes down, then not all our instances go down too. 

## Edge Locations
The idea is that if we have someone in another city, then they might need the data, then we should save a local copy of that data, nearby to them. 
Content Delivery Network, CDNs. 

At AWS, they are called CloudFront. 

Deliver: data, videos, applications, and APIs to customers around the world with low latency and high transfer speeds. 
Edge locations are going to help us do that. 
**They are separate from regions**. 
You can push content from a Region to an Edge Location, around the world, to accelerate communication and content delivery. 

They can run more than CloudFront. 
They can also run, DNS, Amazon Route 53, to direct customers to the correct web locations with reliably low latency. 

AWS Outposts - Mini regions, inside your own data centre. 


## Edge Locations
A site that CloudFront uses to cache copies of data, to be closer to the customer. 
Say we start in Brazil : Then we cache the data close to say China (where the customer is) : When requested, we move the file to the customer. 

## Ways to interact with AWS Services

APIs to interact. 
A contract between a user and the software that we are communicating with, pre-defined ways to configure and manage resources. 
EC2 and Lambda will be different requests: 
AWS Manage Console
AWS Command Line Interafce (CLI)
AWS Software Development Kit (SDK)
### AWS Management Console
Manual provisioning - we might want to script and automate this process (CLI). 

This is a web-based interface, access, manage AWS Services. 
Simple to look at. 
Better for use on bills etc. 
Can include automated workflows, and simplify the process of completing tasks. 
AWS Console Mobile Application, monitor resources, viewing alarms, access billing info. 
Multiple identities can be logged in at any one time. 

### AWS Command Line Interface
Just from the command line (terminal). 
Automate these services through scripts. We could create EC2 instances, and then use a script to gather them all into an Auto Scaling Group. 

### SDK's Software Development Kits
Easy to use in the programming language and platform of our choice. 
Used in our applications, or create entirely new applications that will run on AWS. 
Using languages like C++, Java, .NET. 

