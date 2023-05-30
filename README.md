<img width="794" alt="Screenshot 2023-05-30 at 17 23 57" src="https://github.com/leorickli/wordpress-aws/assets/106999054/925206ac-ced7-4f78-8e3b-61a258e48c03">

# Hosting WordPress on AWS - Full Guide

This is a (almost) free guide that will teach you how to deploy a fully operational WordPress website on AWS cloud infrastructure. I say almost free because we still have to pay for a domain in Route 53 to make the connection via "traditional" ways through a domain name, secured by an SSL certificate.

### Sections

01. **Building a Three-Tiered AWS VPC**:
02. **Creating NAT Gateways**:
03. **Creating the Security Groups**:
04. **Creating the RDS Instances**:
05. **Creating the Elastic File System (EFS)**:
06. **Creating a Key Pair**:
07. **Launcing the Setup Server**:
08. **SSH Into an EC2 Instance in the Public Subnet**:
09. **Installing WordPress**:
10. **Creating an Application Load Balancer and the Private Instances**:
11. **Registering a New Domain Name in Route 53**:
12. **Creating a Record Set in Route 53**:
13. **Registering for an SSL Certificate in AWS Certificate Manager**:
14. **Connecting to AWS Using Session Manager**:
15. **Creating an HTTPS Listener for the Application Load Balancer**:
16. **Creating an Auto Scaling Group**:
