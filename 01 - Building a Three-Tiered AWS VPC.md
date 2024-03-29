## Building a Three-Tiered AWS VPC

![241031154-40b3638a-776c-48de-a4fa-8f0a272325af](https://github.com/leorickli/wordpress-aws/assets/106999054/ea2d438d-22a8-4819-a5ac-a092534a3b82)

*Creating VPCs and subnets provides you with flexibility, control, and security when deploying and managing your resources in the AWS cloud. It allows you to build a customized network infrastructure tailored to your specific needs while maintaining a secure and isolated environment.*

We will start by creating a custom VPC, we will build a three-tiered VPC using the architecture shown above.
In this architecture, your infrastructure is divided into three tiers:

- **Tier 1 - Public Subnet**: This subnet will have resources like the NAT gateway and the load balancer
- **Tier 2 - Private App Subnet**: This is the subnet that will hold our web servers (EC2 instances), managed by an auto scaling group.
- **Tier 3 - Private Data Subnet**: This subnet will hold our database.

We will duplicate these subnets across another Availability Zone (AZ) for High Availability (HA) and fault tolerance. Next, an Internet Gateway (IG) will be created and a Route Table to allow the resources in our VPC to have access to the public internet.

### Creating the VPC
Fist, make sure you select N. Virginia as your region on the AWS console. Search for the VPC service in the search bar and on the VPC page, click on "Create VPC". We have to give a name to the VPC and its IPv4 CIDR, which is the one shown in the diagram above (10.0.0.0/16). Leave the rest as default.

![241002551-60cf93ea-83ad-4669-8cc1-9045a84decdb](https://github.com/leorickli/wordpress-aws/assets/106999054/743e7314-fb29-4b82-bce5-c1a72cc195ee)
![241002634-4c4fab23-52b1-4543-b30e-fc25e2104e94](https://github.com/leorickli/wordpress-aws/assets/106999054/c01f0bba-fd21-4e62-b138-2aae0389757b)

Now enable DNS hostname choosing your VPC and select "Action" > "Edit VPC settings". Check the box that says "Enable DNS hostnames".

![241002195-9684d33a-2cb7-4633-b249-e8db0534d643](https://github.com/leorickli/wordpress-aws/assets/106999054/5e3dd83f-2ef8-431f-b8d0-1559fd48fb6f)

### Creating the Internet Gateway

In the VPC service, select the "Internet gateways" option on the left side, then click "Create Internet Gateway". Just give your Internet Gateway a name for now.

![241005531-f3c78b23-2db5-4968-8cc0-c9cce5b3dd8a](https://github.com/leorickli/wordpress-aws/assets/106999054/5dbefde4-0ed4-4048-aace-3611e6135fe7)

Attach the Internet Gateway to the VPC that we previously created, this way we can allow the VPC to communicate with the internet. Select "Actions" > "Attach to VPC". Select the VPC you created. You can only attach one Internet Gateway to one VPC.

![241005918-8056448d-d7ed-4d27-bc8d-ce6d39b877dd](https://github.com/leorickli/wordpress-aws/assets/106999054/9b8fc67c-5fd5-401a-9e74-4b390c0b959a)

### Creating the public subnets

In the VPC service, select "Subnets" on the left side, then click "Create subnet". Select the VPC you created, give the subnet the name "Public Subnet AZ1", select "us-east-1a" as the AZ and "10.0.0.0/24" as the CIDR block.

![241008856-6e0cfb3e-be65-4761-96d9-d78bfc3edd32](https://github.com/leorickli/wordpress-aws/assets/106999054/c55db029-60d0-4007-808f-abfb0b4c7ab3)
<img width="810" alt="Screenshot 2023-07-22 at 21 18 10" src="https://github.com/leorickli/wordpress-aws/assets/106999054/91fc0394-e65d-421a-bee0-3fe84ce8e2f2">


Now we have to create the second public subnet, to do that, click on "Create Subnet" again and follow the same steps we used previously to create our first public subnet, this time, give it the name "Public Subnet AZ2", select "us-east-1b" as the AZ and give the CIDR block the number "10.0.1.0/24", according to the diagram above.

Next, we will enable the "Auto assign IP settings". This means that every time you launch an EC2 instance in these public subnets, those EC2 instances will be assigned a public IPv4 address. To do that, select one of the public subnet, click "Actions" > "Edit subnet settings". Click on the checkbox that says "Enable auto-assign public IPv4 address".

![241014381-a2eb465b-8114-4cb1-a10d-0d73cf9873b5](https://github.com/leorickli/wordpress-aws/assets/106999054/9c9eeb3e-d968-4f4a-b707-79d31cf0d0d9)

Do the same thing to the other public subnet.

### Creating the Route Table

In the VPC service, select "Route tables" on the left side. You can see an already existing route table in the list, this route table is created by default when you create the VPC. Another thing you should know is that this one is called the "main" route table and it's private by default. We have to create a public route table. To do that, click on "Create route table", give it a name and choose our custom Dev VPC that we just created. On "Tags", give the "Key" a "Name" and for "Value", you can it the same name as the route table.

![241019517-ba7d8594-d822-45a9-a773-5027d2727b5e](https://github.com/leorickli/wordpress-aws/assets/106999054/9cdc93c9-93c9-4cc8-9de6-53cc386a7018)

We have to add a route to the public route table. On the "Routes" tab, click "Edit routes". Click "Add route" and in the "Destination", type "0.0.0.0/0". On "Target", select "Internet Gateway" and select your Internet Gateway. This will allow traffic to the public internet.

![241024598-288b53e4-7af7-41ff-88da-4d3ab7481854](https://github.com/leorickli/wordpress-aws/assets/106999054/dd15bd7a-4d99-4700-903a-d296424343e5)

Now we associate the public subnets to the public route table that we created. On the "Subnet associations" tab, click "Edit subnets associations". Select the two public subnets that we created.

![241027126-109b3b7c-a39d-45ac-9d57-0c04bfba0ae0](https://github.com/leorickli/wordpress-aws/assets/106999054/8b1ad55e-57ad-45af-bb69-4d6572768c8b)

Finally, we will create our last four private subnets, two for the application and two for the database. We just have to repeat the steps when we created the public subnets, but following the diagram above. The difference between a public and private subnet is that you attach a public route table to the public subnet. A route table is public when you add "0.0.0.0/0" to the destination to your Internet Gateway, allowing traffic to all the internet, without restrictions. The other four private subnets will be implicitly associated with the main route table, the one that is created when you create the VPC, but we will create a private route table for those private subnets in the next section.
