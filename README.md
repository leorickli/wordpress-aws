<img width="794" alt="242088891-925206ac-ced7-4f78-8e3b-61a258e48c03" src="https://github.com/leorickli/wordpress-aws/assets/106999054/45fa8e5c-1771-4554-8ba3-7dbf0cce1a55">

# Hosting WordPress on AWS - Full Guide

Checkout [my blog post](https://leorickli.hashnode.dev/deploying-a-wordpress-website-in-aws) to see the personal aspects of doing this project. I garantee you will find something useful there and understand the plethora of knowledge you will acquire by finishing this project.

This is a (almost) free guide that will teach you how to deploy a fully operational WordPress website on AWS cloud infrastructure. I say almost free because we still have to pay for a domain in Route 53 to make the connection via "traditional" ways through a domain name, secured by an SSL certificate.

<img width="876" alt="242095090-684a5db5-cadb-4b77-ad4c-1855d14adb8f" src="https://github.com/leorickli/wordpress-aws/assets/106999054/35b82796-0e9a-4ca5-91ac-712964168ed0">

This is an excelent project to hone your AWS and cloud skills, since all cloud counterparts (GCP, Azure, etc) use similar structure compared to this. You will learn how to use VPCs, subnets, security groups, NAT gateways, route tables, VMs, file systems, databases, create domain names, SSL certificates and IAM roles, making it a great add to your portfolio.

### Resources Used

Amazon VPC; EC2; IAM; EFS; RDS; Route 53; Certificate Manager; Systems Manager.

### Minimum Requirements

A minimum of cloud computing, networking and Linux commands knowledge is necessary to understand and deploy the resources provided in this guide. Some knowledge in the aforementioned AWS resources will be nice to have, but I will try to explain it on their respective sections.

### Sections

01. **Building a Three-Tiered AWS VPC**: Use Amazon VPC to create a custom VPC with six subnets, 2 for each public and private tiers for High Availability (HA) and fault tolerance. Route tables will also be created to direct traffic to the Internet Gateway (IG) for public internet access.
02. **Creating NAT Gateways**: Use Amazon VPC to create NAT gateways and private route tables so our private subnets can also have access to the public internet.
03. **Creating the Security Groups**: Use Amazon VPC to create Security Groups (SG) that controls inbound and outbound traffic for your resources.
04. **Creating the RDS Instances**: Use Amazon RDS to create a database in the private subnets so we can gather login and configuration data from the WordPress website.
05. **Creating the Elastic File System (EFS)**: Use Amazon EFS to allow the webserver and the private app subnets to pull the application code and configuration files from the same location. 
06. **Creating a Key Pair**: Use Amazon EC2 to create a key pair that will SSH into the EC2 instances, allowing connections outside the AWS console.
07. **Launching the Setup Server**: Use Amazon EC2 to create a temporary instance in the public subnet that will install WordPress and store the files in EFS and connect to the RDS database.
08. **SSH Into an EC2 Instance in the Public Subnet**: Make your first connection in your computer through SSH to the EC2 instance.
09. **Installing WordPress**: Using SSH, install Wordpress, store the files on EFS and data on RDS.
10. **Creating an Application Load Balancer and the Private Instances**: Use Amazon EC2 to create an application load balancer that will distribute the connections between the EC2 instances in the two private app subnets.
11. **Registering a New Domain Name in Route 53**: Use Amazon Route 53 to register a new domain name for your website.
12. **Creating a Record Set in Route 53**: Use Amazon Route 53 to create a record set with an alias so we can access our website using our domain name.
13. **Registering for an SSL Certificate in AWS Certificate Manager**: Use AWS Certificate Manager to register for a SSL certificate and use the SSL certificate to encrypt all communications between the web browser and webservers.
14. **Connecting to AWS Using Session Manager**: Use AWS Systems Manager Session Manager and AWS IAM to create a role that will make the connection to the private EC2 instances, removing the need to SSH using key pairs.
15. **Creating a HTTPS Listener for the Application Load Balancer**: Use Amazon EC2 to create a HTTPS listener so we can make secure connections to the website.
16. **Creating an Auto Scaling Group**: Use Amazon EC2 to create an auto scaling group that automatically scale the amount of EC2 instances based on predefined policies and conditions.
