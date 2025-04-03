The cloud is very cool, however, what if we have stuff on-premises, that we want to move to the cloud. 

Cloud Adoption Framework and Snow Family - physical devices used to migrate data in and out of AWS. - snowcone - snowball - snowball edge - snowmobile. 

Migrating to the cloud is a long process. 

AWS  - cloud adoption framework, helping manage this process through guidance. 
Exists to provide advice to your company to enable a quick and smooth migration to AWS. 

Six areas - there will be different people for all of these different topics. 
Business - People - Governance Perspectives - Platform - Security and Operations. 

Someone who is a business or finance analysts would fall under the Business Perspective, HR would fall under the People Perspective, and a cloud architect would fall under the Platform Perspective.

## Six Core Perspective of the Cloud Adoption Framework
CAF 
Each are known as perspectives: 

Business Capabilities: 
1. Business
2. People 
3. Governance 

Technical Capabilities: 
1. Platform
2. Security
3. Operations

**BUSINESS PERSPECTIVES**
**Business Perspective**
IT aligns with business needs, IT investments link to key business results. 
Strong business case for cloud adoption, prioritize cloud adoption initiatives. Business strags and goals align with ITs. 
- Business managers
- Finance maangers
- Budget owners
- Strategy stakeholders
**People Perspective**
Evaluate organizational structures and roles, new skill and process requirements, and identify if we have some gaps there. Prioritizing training, staffing and organizational changes. 
- HR 
- Staffing
- People Managers
**Governance Perspective**
Skills and Processes to align IT strategy with business strats. 
Maximize business value and minimize risks.
Understanding how to update the staff skills and processes necessary to ensure business governance in the cloud. 
Manage and measure cloud investments to evaluate business outcomes. 
- Chief Information Officer (CIO)
- Program managers
- Enterprise architects
- Business Analysts
- Portfolio Managers

**IT PERSPECTIVES**
**Platform Perspective**
Architectural models to understand communicate the structure of IT systems and their relationships. 
- Chief Technology Officer (CTO)
- IT Managers
- Solutions Architects (AKASH!!)
**Security Perspective**
Ensuring we meet security objects for visibility, auditability, control and agility. 
AWS CAF to structure the selection and implementation of security controls, meeting needs: 
- Chief Information Officer (CIO)
- IT security Managers
- IT security analysts
**Operations Perspective**
Day-to-day, quarter-to-quarter, year-to-year business is conducted. 
Align and support operations of the business. 
Helping stakeholders define current operating procedures, identify process changes and training needed to be successful.
- IT operations managers
- IT support managers

## Six R's Of Migration
## Rehosting
Lift and Shift. Pick up the apps, move them to AWS. 
30% cost saving just by rehosting. 
Easier to optimize once they are already in the cloud. 
## Replatforming
Lift, Tinker and Shift. 
Not a one - to - one when we move stuff over. 
We might make a few cloud optimizations. 
Not touching any pure code in the process. 
eg. taking MySQL DB and replatform it onto a RDS MySQL. 
Amazon Aurora. 
No change to major code and its architecture.
## Retire
Some parts of our IT enterprise are no longer needed. 10% to 20% companies application portfolios include applications that are no longer being used or already have replacements live and functional. 
Using AWS migration for end of life of those applications, saving cost and effort on the company's end. 
Sometimes you just have to turn the lights off and those babies. 
Removing applications that are no longer needed. 
## Retain
Maybe not turn all the apps off just yet, maybe they have another 3 or 8 months in them. 
So they don't want to migrate them over. 
Keeping business critical applications in the source environment.
This might be because they require major refactoring before they can be moved over. 
## Repurchase
Abandoning legacy software vendors, getting a fresh start as part of the migration. 
Ending contract with CRM (customer relationship manager) and moving to a brand new one. 
There is a lot of setting up and implementing, which does cost, however, the cost benefits are great when we have them down. 
Going from various licenses, and purchasing what we need for AWS. 

## Refactoring
Writing new code. 
Adding new features or performance that might not be possible on prem, but are not within our reach. 
Changes to architecture can be very beneficial to your enterprise but this will come at the highest initial cost in terms of planning and human effort. 
(re-architecturing)
To use cloud native features.
Strong business need to add features, scale, or performance, otherwise difficult to achive in the application's existing environment. 

- Rehosting
- Replatforming
- Refactoring/re-architecting
- Repurchasing
- Retaining
- Retiring

## AWS Snow Family
Needing to get Data to AWS - as fast and efficiently as possible. 
Copy the data over the internet or better yet, Direct Connect Line. 
This can take forever. 

**AWS Snowcone** Device holds up to 8 TB of data, edge computing. 
EC2 Instances - and AWS IoT Greengrass. 
Place and order via AWS Management Console, it gets shipped over. 
Plug in - copy and ship it back. 
Then AWS copies data to account, S3 buckets that you own. 
Terabytes usually. 

**Snowball Edge** Compute Optimized or Storage Optimize. They fit into existing server racks and can be clustered for greater computing needs. 
Can even run EC2, Lambda Functions - AWS IoT Greengrass, right there and then. 
Ship to remote locations, where it's trickier to have computing power lying around. 
IoT - Internet of Things. 

**Snowmobile** 45 foot shipping container, on a truck. 
It's huge. 
100 petabytes - large migrations - event data centre shutdowns. 
Tamper resistant, waterproof, temperature controlled, fire suppression and GPS tracking. 
Security and 24/7 team on it. 

All of them are secure, they are crypographically signed, all data on it is encrypted, using 256 - bit keys. Owned and Managed by us. 
Even using AWS Key Management Service (KMS) to generate and manage keys here. 


![[Pasted image 20250201211925.png]]
![[Pasted image 20250201211936.png]]
![[Pasted image 20250201211949.png]]![[Pasted image 20250201212012.png]]

## Innovation
The broadest and deepest set of machine learning and AI services for your business. 
Pre-trained AI; for computer visions, language recommendations, and forecasting. 
Amazon SageMaker: Quickly build, train, and deploy machine learning models at scale. 

Build custom models. 
Cloud platforms that are optimized for machine learning, high performance computer and high security. 

SageMaker and Augmented AI, A2I, provide machine learning platform that any business can build upon without needing PhD level expertise in-house. 
Ready-to-go AI solutions like Amazon Lex, the heart of Alexa. 

Textract - Extracting Text and Data from documents to make them more usable for your enterprise instead of them just being locked away in repository. 

DeepRacer - experiment with reinforced learning. 
Newest branches of machine learning algs, all while learning in a racing environment. 

IoT - internet of things - connected devices to communicate all over the world. 

Ground Station - pay for satellite services that you would really need. 

## Innovate
- The current state
- The desired state 
- The problems that you are trying to solve

Consider some of the paths you might explore in the future as you continue on your cloud journey. 

**Serverless Applications**: not requiring us to provision, maintain or administer services. 
No need to worry about fault tolerance or availability. 
AWS handles these for us.

AWS lambda - run serverless applications - with their triggers. 
It means that we can just work on our application, we never need to worry about the server part of this. 


**Artificial Intelligence**: 
AI
- Convert speech to text with Amazon Transcribe
- Find Patterns with Amazon Comprehend
- Potentially Fraudulent online activities with Fraud Detector. 
- Build void and text chatbots with Amazon Lex. 

**Machine Learning**: 
SageMaker - build train and deploy ML models quickly. 

## Amazon Q Developer
Analyzes your comment and code as you write them in your IDE. 
Beyond code completion, by using natural language processing, comprehend the comments in our code. 

Will generate complete functions, aligning with descriptions. 
Matching the style of code around yours. 
Assesses against multiple sets of standards and best practices. 
- OWASP 
- Crypto Lib 
- AWS Security Standards

Might also suggest more than one and then we can pick that. 

Amazon Q Developer learns from open-source projects and the code it suggests might occasionally resemble code samples from the training data. With the reference log, you can view references to code suggestions that are similar to the training data. When such occurrences happen, Amazon Q Developer notifies you and provides repository and licensing information. Use this information to make decisions about whether to use the code in your project and properly attribute the source code as desired.

Looking at open source, then looking at the licensing that we will need for it. 

Helps with boiler plate. 

Developers can look at more critical aspects of the project, and not write the boiler code every single time. 

Developers can easily access Amazon Q Developer by downloading and installing the AWS Toolkit IDE extension. You can also activate Amazon Q Developer from directly within the AWS Lambda and AWS Cloud9 console code editors.