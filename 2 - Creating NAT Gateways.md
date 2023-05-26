## 2 Creating NAT Gateways

![Screenshot 2023-05-25 at 18 47 37](https://github.com/leorickli/wordpress-aws/assets/106999054/8c76ad87-4641-444a-ba51-9c9fdc8c125d)

A NAT Gateway in AWS (Network Address Translation Gateway) is a managed service that allows resources within a private subnet in a Virtual Private Cloud (VPC) to access the internet or other
AWS services, while keeping them hidden behind a single public IP address. The main use of a NAT Gateway is to provide internet connectivity to resources in private subnets that do not have a 
direct internet connection. It acts as a gateway and performs network address translation, allowing private subnet resources to communicate with the internet or other AWS services using the 
NAT Gateway's public IP address. This can be useful for Resources in private subnets, such as EC2 instances, can send requests to the internet (e.g., downloading updates or accessing external APIs) 
through the NAT Gateway.

In this section, we will create a NAT gateway and a private route table for each AZ so the private subnets can make their connetions to the public internet.

To create the NAT gateways, first make sure you are in the same region that you created the VPC, in this case it's the N. Virginia. Then search for the VPC service, on the left side





















