## AWS Shared Responsibility Model
Both user and AWS are responsible for security - it's shared. 

Physical Layer - AWS 100%. 

Third party auditors - network and hypervisor (for VMs) - that will check all of this stuff.  

This is the magic dividing line that separates our responsibility. AWS' responsibility and your responsibility. This is your operating system. You're 100% in charge of this. AWS does not have any backdoor into your system here. You and you alone have the only encryption key to log onto the root of this OS or to create any user accounts there. I mean, no more than a construction company would keep copies of your front door key, AWS cannot enter your operating system. And here's a hint. If someone from AWS calls and asks you for your OS key, it is not AWS.

Now that means your operations team is 100% responsible for keeping the operating system patched. If AWS discovers there are some new vulnerabilities in your version of Windows, let's say, we can certainly notify your account owner but we cannot deploy a patch. This is a really good thing for your security. This means no one can deploy anything that might break your system without your team being the ones that do it. Now, on top of that OS, you can run whatever applications you want. You own them. You maintain them.

The shared responsibility model divides into customer responsibilities (commonly referred to as “security in the cloud”) and AWS responsibilities (commonly referred to as “security of the cloud”).

![[Pasted image 20250130153547.png]]
You can think of this model as being similar to the division of responsibilities between a homeowner and a homebuilder. The builder (AWS) is responsible for constructing your house and ensuring that it is solidly built. As the homeowner (the customer), it is your responsibility to secure everything in the house by ensuring that the doors are closed and locked.

Customers: Security in the cloud

Customers are responsible for the security of everything that they create and put _in_ the AWS Cloud.

  

When using AWS services, you, the customer, maintain complete control over your content. You are responsible for managing security requirements for your content, including which content you choose to store on AWS, which AWS services you use, and who has access to that content. You also control how access rights are granted, managed, and revoked.

The security steps that you take will depend on factors such as the services that you use, the complexity of your systems, and your company’s specific operational and security needs. Steps include selecting, configuring, and patching the operating systems that will run on Amazon EC2 instances, configuring security groups, and managing user accounts.

AWS is responsible for security _of_ the cloud.

AWS operates, manages, and controls the components at all layers of infrastructure. This includes areas such as the host operating system, the virtualization layer, and even the physical security of the data centers from which services operate. 

AWS is responsible for protecting the global infrastructure that runs all of the services offered in the AWS Cloud. This infrastructure includes AWS Regions, Availability Zones, and edge locations.

AWS manages the security of the cloud, specifically the physical infrastructure that hosts your resources, which include:

- Physical security of data centers
- Hardware and software infrastructure
- Network infrastructure
- Virtualization infrastructure

Although you cannot visit AWS data centers to see this protection firsthand, AWS provides several reports from third-party auditors. These auditors have verified its compliance with a variety of computer security standards and regulations.

## User Permissions and Access
Each person has unique access. 
Employees have different sets of permissions and accesses. 

Account Root User - Permission to do anything you want. When you make an account. 
I cannot be restricted in this sense. 
Control any resource, and access anything. Do whatever you want. 

Multi-factor authentication (MFA) - a randomized token to login with this super powerful account. 

Don't use root user for everything. 

Not everyone should be a root user. 

AWS Identity and Access Management (IAM). 
Create IAM users - by default no permissions (can't even log in) super restrictive as it should be - explicitly give the user any and all permissions - by default, everything is denied - access to only what they need - Least Privileged principle - grant only what they need 
An IAM policy - json document - what API calls a user can and cannot make - 
![[Pasted image 20250131002343.png]]
Allow or Deny on Effect
Action : any API call 
Resource : what resource that API is called

They are used all over in AWS accounts. 

Organize them into IAM groups - like with SumUp 
Attach policies to groups - all users of that group will be of that policy. 

Grant all cashiers access to the register using the group. 

Root user - everything
Users - organized into groups
Policies - applied to groups - documents to describe permissions
Roles - assign roles to a user - access might have to change day to day - roles can change at work - Associated permission (allow deny actions) and they can be assumed for temporary amounts of type - temporarily grant access to users, external identities, apps or other AWS services - when we assume a role, we abandon everything we had before. 


![[Pasted image 20250131002726.png]]
The idea is not to use the root user for every day tasks. 

## Users
Identity that we create with AWS. It represents the person or application that interacts with AWS services and resources. 
Name and credentials. 
Default - they have no permissions. 
Grant them permissions. 
Least Permission Principle. 
Create a user for every employee. 

## Policies
Documents that allows or denies permissions to AWS services and resources. 
The least privilege - be very specific. 

## Groups
Collections of users. 
Assign a policy to a group, all users will be assigned that policy. 
Also easier to move someone from a group, to another, rather than re-write all the permissions. 

What if someone has not switched permanently, but rather just for the day, then they can get the access they need through Roles

## Roles
Identity that someone can assume to gain temporary access to permissions. 
Abandon all previous permissions and assume the new ones. 

Owner grants employee permissions to the Cashier and Inventory roles so that they can switch. 

Roles allow the user to switch their permissions, based on what they are dong. 

## MFA Multi-factor Authentication
Extra layer of security. AWS MFA device. 
Enable for the root user for sure, then also for all the workers too. 

## AWS Organizations
One account with everything in - but you want to separate duties. 
Like a business is separated. 
Then all of a sudden we have so many accounts, that is spaghetti af. 
A way to manage multiple accounts - access all the accounts. 


Centralized Management - of all accounts 

Consolidate billing - primary account to pay for all member accounts (bulk discounts)

Hierarchical Groupings - Group accounts by what they are

Control what accounts can access

Service Control Policies (SCPs) - which users can do what. 

## AWS Organizations
There are many accounts now, and we want to manage them in a central location. 
Root - parent container for all organizations. 

SCPs - control permissions for accounts in the orgs. 
Consolidated Billing too. 

## Organizational Units
Easier to manage accounts with similar business or security requirements. 
Accounts in this OU will have similar business or security requirements. 
When we have a policy on an OU, it will apply that policy to all those accounts. 

![[Pasted image 20250131004316.png]]
That's the idea I believe. 

## Compliance
Standards to be upheld - audits - like EHO - Documentation and Records to pass compliance .
You have to make sure your business is up to standard. 

Whatever the compliance need is - we need tools for that. 

AWS did that for us - as a user - we inherit all of those compliant tools, so we only need to worry about our own code. 

Regions have different compliances. 

You own your data
![[Pasted image 20250131004744.png]]
Downloadable documents for compliance - requesting that documentation - that they are following best practices

AWS Artifact - gain access to compliance reports from third parties.

AWS Compliance centre - documentation - risk and security white paper. 

Shared responsibility - build your shit with compliance in mind. 

## AWS Artifact
Provides on-demand access to AWS security and compliance reports and select online agreements. 

## Customer Compliance Centre
For customers to learn more about AWS compliance. 
- AWS answers to key compliance questions
- An overview of AWS risk and compliance
- An auditing security checklist

## Denial-of-Service Attacks
DDOS - Distributed Denial-of-Service
What is the plan? 
What is it? 
It's a long discussion. 
To shut down by overwhelming until non operative - Normal : request and return result 
Overload so that no one else can use the services. 
One person can't do it alone
So they have lots of machines - all attacking at once 
Other bots will all attack at once

UDP Flood 
Anyone can send small request and get some return and then lots of bots can return them as well. 
Giving a fake return address for the return, like your system, then our system has to go through all of that. 

HTTP Level Attacks
An army of bots are requesting so many things, regular customers might not be able to get in. 

Slowloris Attack
Imagine someone in front of you in line is taking forever
Attacker pretends to have a slow connection in order to do this. 
Production is waiting on the request, the entire packet, then everyone else has to wait. 

What do we do: 

UDP Flood - security Groups only allow proper request traffic - not on the list - AWS network level - massive attacks like this, just get shrugged off, not at our EC2 level. 

AWS size is a huge system, therefore, 

Slowloris Attack - Elastic Load Balancer - Scalable running at the region level. Massively expensive to make that work

AWS Shield - AWS WAF - uses web app firewall to filter bad actor behaviour and shield against them (and their signatures). 

## AWS Shield
Protecting against DDoS. 
Two levels or protection: 
Standard and Advanced
Standard - At no cost . Most common attacks. Analysing incoming traffic to detect malicious traffic in real time and mitigating it. 

Advanced - paid service, providing detailed diagnostics and the ability to detect and mitigate sophisticated DDoS attacks. 
Integrating with other services - CloudFront, Route 53, Elastic Load Blaancing. 
AWS WAF - to write custom rules to mitigate complex DDoS attacks. 

## Additional Security Services
Increase security for everything. 
Even in the transport. 
At rest or in transit. 
Encryption - securing message or data in a way that only authorized parties can access. 
If you have the key you can unlock the door. 
Encryption at rest - idle and not moving
Eg. at DynamoDB table is encrypted at rest
Key Management Service - the keys to our resources and their encryptions

In Transit - say between servers 
SSL - secure sockets layer - using certificates to authenticate the clients. 

Inspector - improve security - automated assessment - check on deviations best practices. 
- network configuration reachability piece 
- amazon agent 
- security assessment service
Will give a list of security issues, with recommendations. 

GuardDuty - Threat detection - network activity - looking at metadata - known malicious ip addresses and machine learning to see threats. Won't affect performance of other infrastructures. 

## KMS Key Management Service
Encryption at rest and in transit. 
Perform encryption operations through the use of cryptographic keys. 
Random string of digits for unlocking and locking data. 
With this service we can create, manage and use these keys. 

Control access and which IAM users and roles are able to manage keys. 
Disable keys that are no longer in use by anyone. 

## AWS WAF
Web App Firewall - monitor network requests - working with CloudFront and Load Balancer. 
ACL - access control lists. WAF works similarly to block or allow traffic. 
Through the ACL to protect our AWS Resources. 
![[Pasted image 20250131014348.png]]

Checking against a list of rules to be checked upon usage. 

![[Pasted image 20250131014407.png]]

## Amazon Inspector 
Automated security assessments. 
Giving a list of security findings. 
Shared responsibility model (remember that)> 

## Amazon GuardDuty
Intelligent threat detection. Monitoring the network activity. 
![[Pasted image 20250131014541.png]]

VPC Flow Logs and DNS Logs. 

Producing detailed findings, and putting them AWS Management Console. 

AWS Lambda to do the remediation steps in response to GuardDuty. 

