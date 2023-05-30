## 2 Creating NAT Gateways

![Screenshot 2023-05-25 at 18 47 37](https://github.com/leorickli/wordpress-aws/assets/106999054/8c76ad87-4641-444a-ba51-9c9fdc8c125d)

*A NAT Gateway in AWS (Network Address Translation Gateway) is a managed service that allows resources within a private subnet in a Virtual Private Cloud (VPC) to access the internet or other AWS services, while keeping them hidden behind a single public IP address. The main use of a NAT Gateway is to provide internet connectivity to resources in private subnets that do not have a  direct internet connection. It acts as a gateway and performs network address translation, allowing private subnet resources to communicate with the internet or other AWS services using the  NAT Gateway's public IP address. This can be useful for Resources in private subnets, such as EC2 instances, can send requests to the internet (e.g., downloading updates or accessing external APIs) through the NAT Gateway.*

In this section, we will create a NAT gateway and a private route table for each AZ so the private subnets can make their connetions to the public internet.

### Creating the NAT Gateways

To create the NAT gateways, first make sure you are in the same region that you created the VPC, in this case it's the N. Virginia, then search for the VPC service. In the VPC dashboard, on the left side, select "NAT Gateways", then click "Create NAT gateway" We will create the first NAT gateway in the public subnet AZ1, give it a name. Select a subnet, in this case it will the the first one, the "Public Subnet AZ1", then click on "Allocate Elastic IP"

<img width="814" alt="Screenshot 2023-05-25 at 21 39 07" src="https://github.com/leorickli/wordpress-aws/assets/106999054/001cfc69-c9aa-4c78-b2ee-92a601706aae">

### Creating the Route Tables

The next thing we will do is create the "Private Route Table AZ1". On the VCP dashboard, on the left side of the screen, select "Route Tables", then click "Create route table". Give it a name and select your custom VPC.

<img width="811" alt="Screenshot 2023-05-25 at 21 41 38" src="https://github.com/leorickli/wordpress-aws/assets/106999054/906e7691-4ce9-44d0-b38c-dd2d6c7405e8">

Next, we are going to add a route to the Private Route Table AZ1. On the "Routes" tab, select "Edit routes". Click "Add route", for the "Destination", type the generic internet traffic again "0.0.0.0/0", the "Target" is going to be our NAT Gateway.

<img width="1736" alt="Screenshot 2023-05-25 at 21 47 17" src="https://github.com/leorickli/wordpress-aws/assets/106999054/b41879c8-cc89-42dc-8853-ee788cd46642">

Next, we will associate this Route Table with Private Subnet AZ1 and AZ2, the app and data subnets. In the "Subnet associations" tab, click on "Edit subnet associations". On the "Available subnets", we will select the two subnets that we need.

<img width="1737" alt="Screenshot 2023-05-25 at 21 51 39" src="https://github.com/leorickli/wordpress-aws/assets/106999054/bf2f845e-d30a-473a-a0a1-1246fd0dcba8">

Do the same thing for the second AZ, follow the same steps above and obey he diagram.
