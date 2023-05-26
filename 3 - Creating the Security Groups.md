## Creating the Security Groups

<img width="1044" alt="Screenshot 2023-05-25 at 23 08 43" src="https://github.com/leorickli/wordpress-aws/assets/106999054/be107717-0b37-4f0f-8699-5613ca8ff3e3">

In AWS, a Security Group (SG) is a virtual firewall that controls inbound and outbound traffic for EC2 instances, RDS instances, and other resources within a VPC. It acts as a first line of defense for your resources, allowing you to define inbound and outbound rules to control network access.

In this section we will create multiple Security Groups.

### ALB Security Gruop

The first Security Group we will create is the Application Load Balancer (ALB) SG. We will open port 80 (for HTTP connection) and 443 (for HTTPS connection) and the source is going to come from anywhere on the internet (0.0.0.0/0).

The next Security Group we will create is called the SSH (Secure Shell) SG. This is the SG we will use for the SSH connection on the EC2 instances by opening port 22 (for SSH connection). The source is going to come from your IP address. Everytime you create an SSH SG, you should always limit the source of the traffic to your IP address.

The next Security Group is the Webserver SG. This is the SG we will add to the webservers in the private app subnets. We will open port 80 (for HTTP connection) and 443 (for HTTPS connection) and the source is going to come from the ALB SG. We will also open port 22 (for SSH connection) and the source is going to come from the SSH SG.

The next Security Group is the Database SG. This is the SG we will add to the RDS istance in the private data subnet. We will open port 3306 (for MySQL connection) and the source is going to come from the Webserver SG.

The last Security Group is the EFS SG. This is the SG we will add to the EFS resource. We will open port 2049 (for NFS (Network File System) protocol connection) and the source is going to come from the Webserver SG and the EFS SG. We are also going to open port 22 and the source is going to come from the SSH SG.
