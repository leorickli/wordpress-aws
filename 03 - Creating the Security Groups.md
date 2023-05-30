## Creating the Security Groups

<img width="1044" alt="Screenshot 2023-05-25 at 23 08 43" src="https://github.com/leorickli/wordpress-aws/assets/106999054/be107717-0b37-4f0f-8699-5613ca8ff3e3">

*In AWS, a Security Group (SG) is a virtual firewall that controls inbound and outbound traffic for EC2 instances, RDS instances, and other resources within a VPC. It acts as a first line of defense for your resources, allowing you to define inbound and outbound rules to control network access.*

In this section we will create multiple Security Groups:

- ALB SG
- SSH SG
- Webserver SG
- Database SG
- EFS SG

### ALB SG

![Screenshot 2023-05-26 at 14 04 18](https://github.com/leorickli/wordpress-aws/assets/106999054/d5c35f11-145a-409a-b7fd-fcfc0537b81a)

The first Security Group we will create is the Application Load Balancer (ALB) SG. We will open port 80 (for HTTP connection) and 443 (for HTTPS connection) and the source is going to come from anywhere on the internet (0.0.0.0/0).

On VPC dashboard that is on the left side, select "Security groups", click "Create security group". Give it a name and the same name for the description, select your custom Dev VPC that you created. On "Inbound rules", click "Add rule", we will add two ports:

1. Type: HTTP (port 80). Source: Anywhere-IPv4 (0.0.0.0/0)
2. Type: HTTPS (port 443). Source: Anywhere-IPv4 (0.0.0.0/0)

It's worth noting that, since SGs are stateful (when you allow inbound traffic, the corresponding outbound traffic is automatically allowed) you do not need to add an explicit outbound rule to allow connection traffic when you have already added an inbound rule in your security group.

![Screenshot 2023-05-26 at 14 43 24](https://github.com/leorickli/wordpress-aws/assets/106999054/195c45ab-bc10-4f51-92ae-6f32a01d9f02)

### SSH SG

The next Security Group we will create is called the SSH (Secure Shell) SG. This is the SG we will use for the SSH connection on the EC2 instances by opening port 22 (for SSH connection). The source is going to come from your IP address. Every time you create an SSH SG, you should always limit the source of the traffic to your IP address.

On VPC dashboard that is on the left side, select "Security groups", click "Create security group". Give it a name and the same name for the description, select your custom Dev VPC that you created. On "Inbound rules", click "Add rule", we will add one port:

1. Type: SSH (port 22). Source: My IP

![Screenshot 2023-05-26 at 14 51 46](https://github.com/leorickli/wordpress-aws/assets/106999054/ee5b6401-ef8e-4d0a-82ab-d2256b194105)

### Webserver SG

![Screenshot 2023-05-26 at 14 32 13](https://github.com/leorickli/wordpress-aws/assets/106999054/4e4e6ea0-3fe6-42c9-9316-349d37ed2a28)

The next Security Group is the Webserver SG. This is the SG we will add to the webservers in the private app subnets. We will open port 80 (for HTTP connection) and 443 (for HTTPS connection) and the source is going to come from the ALB SG. We will also open port 22 (for SSH connection) and the source is going to come from the SSH SG.

On VPC dashboard that is on the left side, select "Security groups", click "Create security group". Give it a name and the same name for the description, select your custom Dev VPC that you created. On "Inbound rules", click "Add rule", we will add three ports:

1. Type: HTTP (port 80). Source: Custom and select ALB SG
2. Type: HTTPS (port 443). Source: Custom and select ALB SG
3. Type: HTTP (port 22). Source: Custom and select SSH SG

![Screenshot 2023-05-26 at 14 55 52](https://github.com/leorickli/wordpress-aws/assets/106999054/d34f95e3-82fe-4756-b870-9ef787ce9dbd)

### Database SG

![Screenshot 2023-05-26 at 14 34 18](https://github.com/leorickli/wordpress-aws/assets/106999054/76f74ce6-4e8e-4bcb-9f7f-6b4a7ff7f168)

The next Security Group is the Database SG. This is the SG we will add to the RDS istance in the private data subnet. We will open port 3306 (for MySQL connection) and the source is going to come from the Webserver SG.

On VPC dashboard that is on the left side, select "Security groups", click "Create security group". Give it a name and the same name for the description, select your custom Dev VPC that you created. On "Inbound rules", click "Add rule", we will add one port:

1. Type: MYSQL/Aurora (port 3306). Source: Custom and select Webserver SG

![Screenshot 2023-05-26 at 15 07 15](https://github.com/leorickli/wordpress-aws/assets/106999054/73ef3e53-5031-48cc-aec5-9bdc58168462)

### EFS SG

![Screenshot 2023-05-26 at 14 35 11](https://github.com/leorickli/wordpress-aws/assets/106999054/b38d1ca9-d565-4fa2-8520-33619430c080)

The last Security Group is the EFS SG. This is the SG we will add to the EFS resource. We will open port 2049 (for NFS (Network File System) protocol connection) and the source is going to come from the Webserver SG and the EFS SG. We are also going to open port 22 and the source is going to come from the SSH SG.

On VPC dashboard that is on the left side, select "Security groups", click "Create security group". Give it a name and the same name for the description, select your custom Dev VPC that you created. On "Inbound rules", click "Add rule", we will add two ports:

1. Type: NFS (port 2049). Source: Custom and select Webserver SG
2. Type: SSH (port 22). Source: Custom and select SSH SG

![Screenshot 2023-05-26 at 15 11 02](https://github.com/leorickli/wordpress-aws/assets/106999054/30680c47-5e93-4545-8996-25c58e5f55b6)

After you created the SG, on the "Inbound rules" tab, select "Edit inbound rules". Add one more port:

1. Type: NFS (port 2049). Source: Custom and select EFS SG

![Screenshot 2023-05-26 at 15 19 32](https://github.com/leorickli/wordpress-aws/assets/106999054/a28bb819-a8c0-4e31-9775-f662446e0ced)

The reason for adding a rule to an EFS security group to allow it to contact itself is to allow the various instances or resources that are associated with the security group to access the EFS file system. Without this rule, the instances or resources would not be able to communicate with the EFS file system and would not be able to access or store files on it. By allowing the SG to contact itself, you are essentially creating a permission for the resources within the security group to communicate with the EFS file system. This is an important step in the process of setting up and using an EFS file system.
