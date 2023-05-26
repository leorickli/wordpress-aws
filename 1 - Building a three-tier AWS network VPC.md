## Building a three-tier AWS network VPC

![Screenshot 2023-05-25 at 16 52 43](https://github.com/leorickli/wordpress-aws/assets/106999054/40b3638a-776c-48de-a4fa-8f0a272325af)

We will start by creating a custom VPC, we will build a three-tier VPC using the architecture shown above.
In this architecture, your infrastructure is divided into three tiers:

- Tier 1: This is the public subnet. This subnet will have resources like the NAT gateway, load balancer and a bastion host.
- Tier 2: This is the private subnet. This is the subnet that will hold our web servers (EC2 instances).
- Tier 3: This is another private subnet. This subnet will hold our database.

We will duplicate these subnets across another availability zone (AZ) for High Availability (HA) and Fault Tolerance (FA). Next, an Internet Gateway will be created and a Route Table to allow the resources in our VPC to have access to the public internet.

### Creating the VPC
Fist, make sure you select N. Virginia as your region on the console. Search for the VCP service in the search bar and click on "Create VPC". Fill the spaces accordingly to the image below, we have to give a name to the VPC and its IPv4 CIDR, which is the one shown in the diagram above (10.0.0.0/16). Leave the rest as default.

![Screenshot 2023-05-25 at 15 25 45](https://github.com/leorickli/wordpress-aws/assets/106999054/60cf93ea-83ad-4669-8cc1-9045a84decdb)
![Screenshot 2023-05-25 at 15 26 13](https://github.com/leorickli/wordpress-aws/assets/106999054/4c4fab23-52b1-4543-b30e-fc25e2104e94)


Now enable DNS hostname choosing your VPC and select "Action" > "Edit VCP settings". Check the box that says "Enable DNS hostnames"

![Screenshot 2023-05-25 at 15 23 58](https://github.com/leorickli/wordpress-aws/assets/106999054/9684d33a-2cb7-4633-b249-e8db0534d643)

### Creating the Internet Gateway

In the VPC service, select the "Internet gateways" option on the left side, then click "Create Internet Gateway". Just give your Internet Gateway a name for now.

![Screenshot 2023-05-25 at 15 40 07](https://github.com/leorickli/wordpress-aws/assets/106999054/f3c78b23-2db5-4968-8cc0-c9cce5b3dd8a)

Attach the Internet Gateway to the VPC that we previously created, this way we can allow the VPC to communicate with the internet. Select "Actions" > "Attach to VPC". Select the VPC you created. You can only attach one Internet Gateway to one VPC.

![Screenshot 2023-05-25 at 15 42 01](https://github.com/leorickli/wordpress-aws/assets/106999054/8056448d-d7ed-4d27-bc8d-ce6d39b877dd)

### Creating the public subnets

In the VPC service, select "Subnets" on the left side, then click "Create subnet". Select the VPC you created, give the subnet the name "Public Subnet AZ1", select "us-east-1a" as the AZ and "10.0.0.0/24" as the CIDR block.

![Screenshot 2023-05-25 at 15 56 26](https://github.com/leorickli/wordpress-aws/assets/106999054/6e0cfb3e-be65-4761-96d9-d78bfc3edd32)
![Screenshot 2023-05-25 at 15 57 10](https://github.com/leorickli/wordpress-aws/assets/106999054/91a4067f-6d7a-48f8-a4e7-a3f7d70a40d7)

Now we have to create the second public subnet, to do that, click on "Create Subnet" again and follow the same steps we used previously to create our first public subnet,
but give it the name "Public Subnet AZ2", select "us-east-1b" as the AZ and give the CIDR block the number "10.0.1.0/24", according to the diagram above.

Next, we will enabe the "Auto assign IP settings". This means that everytime you launch an EC2 instance in these public subnets, those EC2 instances will be assigned a public IPv4 address. To do that, select one of the public subnett, click "Actions" > "Edit subnet settings". Click on the checkbox that says "Enable auto-assign public IPv4 address".

![Screenshot 2023-05-25 at 16 20 57](https://github.com/leorickli/wordpress-aws/assets/106999054/a2eb465b-8114-4cb1-a10d-0d73cf9873b5)

Do the same thing to the other public subnet.

### Creating the Route Table

In the VPC service, select "Route tables" on the left side. You can see an already existing route table in the list, this route table is created by default when you create the VPC. Another thing you should know is that this one is called the "main" route table and it's private by default. We have to create a public rout table. To do that, click on "Create route table" and fill it with the info below

![Screenshot 2023-05-25 at 16 30 38](https://github.com/leorickli/wordpress-aws/assets/106999054/ba7d8594-d822-45a9-a773-5027d2727b5e)

We have to add a route to the public route table. On the "Routes" tab, click "Edit routes". Click "Add route" and in the "Destination", type "0.0.0.0/0". On "Target", select "Internet Gateway" and select your Internet Gatewayl. This will allow traffic to the public internet

![Screenshot 2023-05-25 at 16 40 03](https://github.com/leorickli/wordpress-aws/assets/106999054/288b53e4-7af7-41ff-88da-4d3ab7481854)

Now we associate the public subnets to the public route table that we created. On the "Subnet associations" tab, click "Edit subnets associations". Select the two public subnets that we created.

![Screenshot 2023-05-25 at 16 44 43](https://github.com/leorickli/wordpress-aws/assets/106999054/109b3b7c-a39d-45ac-9d57-0c04bfba0ae0)

Finally, we will create our last four private subnets, two for the application and two for the database. We just have to repeat the steps when we created the public subnets, but following the diagram above. The difference between a public and private subnet is that you attach a public route table to the public subnet. A route table is public when you add "0.0.0.0/0" to the destination to your Internet Gateway, allowing traffic to all of the internet, without restrictions. The other four private subnets will be implicitly associated with the main route table, the one that is created when you create the VPC, but we will create a private route table for those private subnets in the next section
