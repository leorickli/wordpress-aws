## Creating the Security Groups

<img width="1044" alt="241100925-be107717-0b37-4f0f-8699-5613ca8ff3e3" src="https://github.com/leorickli/wordpress-aws/assets/106999054/faf280c5-436b-405c-9c94-bb4e988ca9d1">

*In AWS, a Security Group (SG) is a virtual firewall that controls inbound and outbound traffic for EC2 instances, RDS instances, and other resources within a VPC. It acts as a first line of defense for your resources, allowing you to define inbound and outbound rules to control network access.*

In this section we will create multiple Security Groups:

- ALB SG
- SSH SG
- Webserver SG
- Database SG
- EFS SG

### ALB SG

![241280803-d5c35f11-145a-409a-b7fd-fcfc0537b81a](https://github.com/leorickli/wordpress-aws/assets/106999054/d89540fd-99d0-48c9-ad79-303b9cfbd7f5)

The first Security Group we will create is the Application Load Balancer (ALB) SG. We will open port 80 (for HTTP connection) and 443 (for HTTPS connection) and the source is going to come from anywhere on the internet (0.0.0.0/0).

Search for VPC on the AWS console. On the VPC dashboard, select "Security groups", click "Create security group". Give it a name and the same name for the description, select your custom Dev VPC that you created. On "Inbound rules", click "Add rule", we will add two ports:

1. Type: HTTP (port 80). Source: Anywhere-IPv4 (0.0.0.0/0)
2. Type: HTTPS (port 443). Source: Anywhere-IPv4 (0.0.0.0/0)

It's worth noting that, since SGs are stateful (when you allow inbound traffic, the corresponding outbound traffic is automatically allowed) you do not need to add an explicit outbound rule to allow connection traffic when you have already added an inbound rule in your security group.

![241287898-195c45ab-bc10-4f51-92ae-6f32a01d9f02](https://github.com/leorickli/wordpress-aws/assets/106999054/bdaa95a0-fc4b-46fd-adab-72a790154230)

### SSH SG

The next Security Group we will create is called the SSH (Secure Shell) SG. This is the SG we will use for the SSH connection on the EC2 instances by opening port 22 (for SSH connection). The source is going to come from your IP address. Every time you create an SSH SG, you should always limit the source of the traffic to your IP address.

On VPC dashboard that is on the left side, select "Security groups", click "Create security group". Give it a name and the same name for the description, select your custom Dev VPC that you created. On "Inbound rules", click "Add rule", we will add one port:

1. Type: SSH (port 22). Source: My IP

![242113534-2c8c5a5e-cea8-4ca5-bf72-262df903b11c](https://github.com/leorickli/wordpress-aws/assets/106999054/212d0956-8a03-427d-8b90-9926226d045e)

### Webserver SG

![241286101-4e4e6ea0-3fe6-42c9-9316-349d37ed2a28](https://github.com/leorickli/wordpress-aws/assets/106999054/60bc8e78-88e3-422b-ae42-1c6578448974)

The next Security Group is the Webserver SG. This is the SG we will add to the webservers in the private app subnets. We will open port 80 (for HTTP connection) and 443 (for HTTPS connection) and the source is going to come from the ALB SG. We will also open port 22 (for SSH connection) and the source is going to come from the SSH SG.

On VPC dashboard that is on the left side, select "Security groups", click "Create security group". Give it a name and the same name for the description, select your custom Dev VPC that you created. On "Inbound rules", click "Add rule", we will add three ports:

1. Type: HTTP (port 80). Source: Custom and select ALB SG
2. Type: HTTPS (port 443). Source: Custom and select ALB SG
3. Type: HTTP (port 22). Source: Custom and select SSH SG

![241290357-d34f95e3-82fe-4756-b870-9ef787ce9dbd](https://github.com/leorickli/wordpress-aws/assets/106999054/1c21e83e-97f9-44f2-b246-81e9d1124bcc)

### Database SG

![241286380-76f74ce6-4e8e-4bcb-9f7f-6b4a7ff7f168](https://github.com/leorickli/wordpress-aws/assets/106999054/84f88b29-251d-4293-815a-ae3aa85631b7)

The next Security Group is the Database SG. This is the SG we will add to the RDS istance in the private data subnet. We will open port 3306 (for MySQL connection) and the source is going to come from the Webserver SG.

On VPC dashboard that is on the left side, select "Security groups", click "Create security group". Give it a name and the same name for the description, select your custom Dev VPC that you created. On "Inbound rules", click "Add rule", we will add one port:

1. Type: MYSQL/Aurora (port 3306). Source: Custom and select Webserver SG

![241292576-73ef3e53-5031-48cc-aec5-9bdc58168462](https://github.com/leorickli/wordpress-aws/assets/106999054/405862fc-4996-40fe-8d65-43fccb9c1faf)

### EFS SG

![241286488-b38d1ca9-d565-4fa2-8520-33619430c080](https://github.com/leorickli/wordpress-aws/assets/106999054/6c92383a-9a0d-4255-b306-a5005c188c36)

The last Security Group is the EFS SG. This is the SG we will add to the EFS resource. We will open port 2049 (for NFS (Network File System) protocol connection) and the source is going to come from the Webserver SG and the EFS SG. We are also going to open port 22 and the source is going to come from the SSH SG.

On VPC dashboard that is on the left side, select "Security groups", click "Create security group". Give it a name and the same name for the description, select your custom Dev VPC that you created. On "Inbound rules", click "Add rule", we will add two ports:

1. Type: NFS (port 2049). Source: Custom and select Webserver SG
2. Type: SSH (port 22). Source: Custom and select SSH SG

![241293306-30680c47-5e93-4545-8996-25c58e5f55b6](https://github.com/leorickli/wordpress-aws/assets/106999054/3a36d1dd-930f-4606-b8af-065795b6bbfd)

After you created the SG, on the "Inbound rules" tab, select "Edit inbound rules". Add one more port:

1. Type: NFS (port 2049). Source: Custom and select EFS SG

![241294829-a28bb819-a8c0-4e31-9775-f662446e0ced](https://github.com/leorickli/wordpress-aws/assets/106999054/ceba92e5-44c0-4958-8bc4-840605ecbbb5)

The reason for adding a rule to an EFS security group to allow it to contact itself is to allow the various instances or resources that are associated with the security group to access the EFS file system. Without this rule, the instances or resources would not be able to communicate with the EFS file system and would not be able to access or store files on it. By allowing the SG to contact itself, you are essentially creating a permission for the resources within the security group to communicate with the EFS file system. This is an important step in the process of setting up and using an EFS file system.
