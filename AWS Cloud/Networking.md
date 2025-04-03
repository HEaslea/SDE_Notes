Amazon Virtual Private Cloud, VPCs. 
Provision a logically isolated section of the AWS cloud, launch AWS resources in a virtual network that you define. 

Public and Private grouping of resources, known as subnets, and they are ranges of IP addresses in your VPC. 

Cashier in our coffee shop is the public subnet. 
Baristas in the private subnet. 

## VPC
Own private network in the AWS space. 

defining own private IP range for your AWS resources. 
Then you put EC2 in there, and ELBs. 

Place into differing subnets. 
They are chunks of IP addresses in your VPC that allow you to group together resources. 

Then we can control which traffic gets into our VPC. 

Might want a public facing thing. 

To flow into and out of our VPC, we must have an internet gateway, IGW, to the VPC. 

It's a gateway, like a door to the public. 

Then, what if we want to go private: allowing only people from an approved network. 
Virtual Private Gateway. 
So if you want to establish an encrypted VPN connection to your private internal AWS resources, you would need to attach a virtual private gateway to your VPC.

With AWS, you can achieve that using what is called AWS Direct Connect. Direct Connect allows you to establish a completely private, dedicated fiber connection from your data center to AWS. You work with a Direct Connect partner in your area to establish this connection, because like my magic doorway, AWS Direct Connect provides a physical line that connects your network to your AWS VPC

## Amazon Virtual Private Cloud VPC
Imagine the millions of customers who use AWS services. Also, imagine the millions of resources that these customers have created, such as Amazon EC2 instances. Without boundaries around all of these resources, network traffic would be able to flow between them unrestricted.
A networking service that you can use to establish boundaries around your AWS resources is [**Amazon Virtual Private Cloud (Amazon VPC)**(opens in a new tab)](https://aws.amazon.com/vpc/).
We can organize into subnets, different sections. 

## Internet Gateway
To allow those from the internet into the VPC, we get an **internet gateway**. 
![[Pasted image 20250130034043.png]]An internet gateway is a connection between a VPC and the internet. You can think of an internet gateway as being similar to a doorway that customers use to enter the coffee shop. Without an internet gateway, no one can access the resources within your VPC.

## Virtual Private Gateway
To access private resources in a VPC, you can use a **virtual private gateway**.

Here’s an example of how a virtual private gateway works. You can think of the internet as the road between your home and the coffee shop. Suppose that you are traveling on this road with a bodyguard to protect you. You are still using the same road as other customers, but with an extra layer of protection.

The bodyguard is like a virtual private network (VPN) connection that encrypts (or protects) your internet traffic from all the other requests around it.

The virtual private gateway is the component that allows protected internet traffic to enter into the VPC. Even though your connection to the coffee shop has extra protection, traffic jams are possible because you’re using the same road as other customers.

![[Pasted image 20250130034257.png]]

The gateway allows for the VPN, from VPC and private network, eg. on-premises data centre or internal corporate network. 
Virtual private gateway allows only approved network traffic. 

## AWS Direct Connect

[**AWS Direct Connect**(opens in a new tab)](https://aws.amazon.com/directconnect/) is a service that lets you to establish a dedicated private connection between your data center and a VPC.
Suppose that there is an apartment building with a hallway directly linking the building to the coffee shop. Only the residents of the apartment building can travel through this hallway. 

This private hallway provides the same type of dedicated connection as AWS Direct Connect. Residents are able to get into the coffee shop without needing to use the public road shared with other customers.

![[Pasted image 20250130034427.png]]

## Subnets and Network Access Control Lists
VPC - hardened fortress, nothing in or out without explicit permission. 
Network hardening, application security, user identity, authentication and authorization, distributed denial-of-service or DDoS prevention, data integrity, encryption, much more

The public subnets have access to the internet gateway; the private subnets do not.

Packets are messages from the internet, and every packet that crosses the subnet boundaries gets checked against something called a network access control list or network ACL
This check is to see if the packet has permissions to either leave or enter the subnet based on who it was sent from and how it's trying to communicate.
Passport Control Officers. 

The list gets checked on your way into a country and on the way out. And just because you were let in doesn't necessarily mean they're gonna let you out. 

Approved traffic can be sent on its way, and potentially harmful traffic, like attempts to gain control of a system through administrative requests, they get blocked before they ever touch the target. Can't hack what you can't touch. 

Because a network ACL only gets to evaluate a packet if it crosses a subnet boundary, in or out.

To solve instance level access questions, we introduce security groups. As in for every EC2 there may be more security.  Every EC2 instance, when it's launched, automatically comes with a security group. 
And by default, the security group does not allow any traffic into the instance at all. All ports are blocked; all IP addresses sending packets are blocked.

Then we modify that instance to accept certain type of traffic. 

NACL - network access control list. 
If NACLs are a passport control, a security group is like the doorman at your building, the building being the EC2 Instance, in this case. 

The key difference between a security group and a network ACL is the security group is stateful, meaning, as we talked about, it has some kind of a memory when it comes to who to allow in or out, and the network ACL is stateless, which remembers nothing and checks every single packet that crosses its border regardless of any circumstances.

Security Groups remember, like a doorman letting you back in, when they have seen you leave the club. 
However the passport guys need to check every single time. 

All right. Let's start with instance A. We wanna send a packet from instance A to instance B in a different subnet, same VPC, different subnets. 
So instance A sends the packet. Now, the first thing that happens is that packet meets the boundary of the security group of instance A. By default, all outbound traffic is allowed from a security group. So you can walk right by the doormen and leave, cool, right. The packet made it past the security group of instance A. Now it has to leave the subnet boundary. At the boundary, the passport must now make it through passport control, the network ACL. The network ACL doesn't care about what the security group allowed. It has its own list of who can pass and who can't. If the target address is allowed, you can keep going on your journey, which it is.
Each EC2 has their own subgroup. 
Once the transaction's complete, now it's just time to come home. It's the return traffic pattern. It's the most interesting, because this is where the stateful versus stateless nature of the different engines come into play. Because the packet still has to be evaluated at each checkpoint. Security groups, by default, allow all return traffic. So they don't have to check a list to see if they're allowed out. Instead, they automatically allow the return traffic to pass by no matter what. Passport control here at the subnet boundary, these network ACLs do not remember state. They don't care that the packet came in here. It might be on a you-can't-leave list. Every ingress and egress is checked with the appropriate list. The package return address has to be on their approved list to make it across the border. Made it to the border of the origin subnet, but we have to negotiate passport network ACL control here as well. Stateless controls, means it always checks its list.

![[Pasted image 20250130134201.png]]
Private Access to the baristas. 

![[Pasted image 20250130134251.png]]Private Subnets - customer data etc. 


## **Network traffic in a VPC**

When a customer requests data from an application hosted in the AWS Cloud, this request is sent as a packet. A **packet** is a unit of data sent over the internet or a network. 

It enters into a VPC through an internet gateway. Before a packet can enter into a subnet or exit from a subnet, it checks for permissions. These permissions indicate who sent the packet and how the packet is trying to communicate with the resources in a subnet.

The VPC component that checks packet permissions for subnets is a [**network access control list (ACL)**(opens in a new tab)](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html).

## NACL - Network Access Control List

![[Pasted image 20250130134332.png]]


A network ACL is a virtual firewall that controls inbound and outbound traffic at the subnet level.
**At the subnet level**. 

Stateless - they remember nothing, everyone has to be checked against the list at all time. 
As you go out, and as you come in. 
Each AWS account includes a default network ACL. When configuring your VPC, you can use your account’s default network ACL or create custom network ACLs.By default, your account’s default network ACL allows all inbound and outbound traffic, but you can modify it by adding your own rules. For custom network ACLs, all inbound and outbound traffic is denied until you add rules to specify which traffic to allow. Additionally, all network ACLs have an explicit deny rule. This rule ensures that if a packet doesn’t match any of the other rules on the list, the packet is denied.

![[Pasted image 20250130134719.png]]

## Security Groups

![[Pasted image 20250130134743.png]]

Virtual firewall. 
By default, a security group denies all inbound traffic and allows all outbound traffic. You can add custom rules to configure which traffic should be allowed; any other traffic would then be denied. 
Deny all inbound, allow all outbound. 

For this example, suppose that you are in an apartment building with a door attendant who greets guests in the lobby. You can think of the guests as packets and the door attendant as a security group. As guests arrive, the door attendant checks a list to ensure they can enter the building. However, the door attendant does not check the list again when guests are exiting the building. 

Literally a door man. 

![[Pasted image 20250130134923.png]]![[Pasted image 20250130134931.png]]

## VPC Component Recall
Private Subnet - Isolate Databases containing customer's personal information. 

VPG - Virtual Private Gateway - Create a VPN connection between the VPC and the internal corporate network. 

Public Subnet - Support the customer facing website. 

Amazon Direct Connect - Establish a dedicated connection between the on-premises data centre and the VPC.

## Global Networking
Route 53 - DNS - Domain Name Service, highly available and scalable. 
DNS - Translation service, website names into IP, Internet Protocol, addresses that computers can read. 

Type name, get IP, route to that address. 
Route 53 can direct as well, with different policies, geolocation DNS, geoproximity, and weighted round robin. 

You can buy and manage your own domain names on route 53. 

Amazon CloudFront, Edge locations. CDN - content delivery network. 
Deliver edge content to users based on geographic location. 
If we go back to our North America versus Ireland example, say we have a user in Seattle, and they want to access a website, to speed this up, we host the site in Oregon, and we deploy our static web assets, like images and GIFs in CloudFront in North America. This means they get content delivered as close to them as possible, North America in this case, when they access the site. But for our Irish users, it doesn't make sense to deliver those assets out of Oregon, as the latency is not favorable. Thus we deploy those same static assets in CloudFront, but this time in the Dublin Region. That means they can access the same content, but from a location closer to them, which in turn improves latency.

## DNS Domain Name System 

![[Pasted image 20250130135840.png]]

Suppose that AnyCompany has a website hosted in the AWS Cloud. Customers enter the web address into their browser, and they are able to access the website. This happens because of **Domain Name System (DNS)** resolution. DNS resolution involves a customer DNS resolver communicating with a company DNS server.
You can think of DNS as being the phone book of the internet. DNS resolution is the process of translating a domain name to an IP address.

## Amazon Route 53
[**Amazon Route 53**(opens in a new tab)](https://aws.amazon.com/route53) is a DNS web service. It gives developers and businesses a reliable way to route end users to internet applications hosted in AWS.

Amazon Route 53 connects user requests to infrastructure running in AWS (such as Amazon EC2 instances and load balancers). It can route users to infrastructure outside of AWS.

![[Pasted image 20250130140129.png]]
Customer requests data from the app by going to AnyComp's website. 
Route 53 uses DNS resolution, to get the IP, that's sent back to the customer. 

The customer’s request is sent to the nearest edge location through Amazon CloudFront.

Amazon CloudFront connects to the Application Load Balancer, which sends the incoming packet to an Amazon EC2 instance.