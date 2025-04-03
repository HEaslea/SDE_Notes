I want to view the data of the coffee shop. 

Number of coffees, average wait time, running out of inventory. 

Alerted if wait times become too long. 

Every business can use metrics to measure. 

Using metrics to make decisions - Monitoring 

Scaling based on Monitoring. 

With the elastic nature of the services - dynamically scaling up and down. 

## Amazon CloudWatch
Monitor and Manage various metrics, configure alarm actions based on data from those metrics. 
Metrics to represent the data points for your resources. 
AWS Services send metrics to cloudwatch. 
Creates graphs, showing how performance changes over time. 

**Alarms**: 
Creating alarms that perform actions if the value of your metric has gone above or below a predefined threshold. 

![[Pasted image 20250131015431.png]]Can even has separate dashboards. Looks so much like sumUP. 

## AWS CloudTrail 
Trust but verify. 
Changing my accident - doesn't have to be malicious. 

API Auditing Tool - every request made to AWS - logged in the CloudTrail engine (monitoring the trail), they record everything about that request. 

CloudTrail will show things like not changing settings - for an auditor. 
Tamperproof vault - absolute evidence that no one can change anything about this. 
The trail of breadcrumbs. 

Monitoring API calls. 

Can filter events - they are usually uploaded after 15 mins. 
![[Pasted image 20250131015751.png]]

**CloudTrail Insights** - automatically detect unusual API activities in your AWS Account. 

## AWS Trusted Advisors
Someone to give just good solid advice. 

Someone to know what to look for - what do we change? (Save some money)

Will act on five pillars 
- Cost Optimization
- Performance 
- Security
- Fault tolerance 
- Service Limits 
Running through checks based on these - and AWS best practices and creating items to look at for us. 

Some are free 

Others are priced

eg. MFA for root user, it will let you know

Under-utilised EC2 instances that we can turn off. 

Too late on a DB Backup.

With potential monthly savings. 

Will give a list that will provide every note that we could use. 

Will work on AWS Best Practices. 

![[Pasted image 20250131020344.png]]

No problems with that part of that pillar
Investigations to be made 
Red - actions required. 

