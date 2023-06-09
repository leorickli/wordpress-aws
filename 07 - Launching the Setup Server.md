## Launching the Setup Server

<img width="820" alt="241339718-327fbbda-e889-450b-a129-1fdec8d70556" src="https://github.com/leorickli/wordpress-aws/assets/106999054/1a6affdd-3ec7-47eb-a8c0-35ebe4688f4e">

I this section, we will launch the EC2 instance that will install WordPress in the public subnet AZ1. We are just creating this instance for temporary purposes, we need to install WordPress on EC2 and save the code to EFS, so we do it in the public subnet. We will deploy the Setup Server in the public subnet for ease of installation and file transfer to EFS.

In the AWS console, search for EC2. On the EC2 dashboard, in the "Resources" tab, select "Instances (running)" then "Launch Instances". Give it a name, one the "Application and OS Images (Amazon Machine Images), select "Amazon Linux 2 AMI (HVM)", this is the free tier AMI. Be careful not to choose "Amazon Linux 2023", even though this one is also free tier, it will not work when we try to install WordPress in it.

<img width="785" alt="241450978-eb722a29-29ec-4105-917e-92bc1f5ff8e1" src="https://github.com/leorickli/wordpress-aws/assets/106999054/50aaf929-f72d-4027-8bc4-51977a08ccfd">
<img width="786" alt="241450981-4d26cbd2-befa-49bc-88f0-be7e5a87c724" src="https://github.com/leorickli/wordpress-aws/assets/106999054/cd2fe348-95d9-402c-b1d9-0d1084d6fdd6">

On "Instance type", make sure that "t2.micro" is selected to keep up with the free tier option. On the "Key pair", select the key pair that you created.

<img width="787" alt="241338129-2a0f0311-0827-48d2-9bda-c01d45873ba1" src="https://github.com/leorickli/wordpress-aws/assets/106999054/f61cafbf-f77d-4745-ad7d-a6b85cfc6f50">

On "Network settings", click "Edit". Select your custom Dev VPC and for the subnet, select the public subnet AZ1, this is where we will deploy our EC2 instance, on "Firewall (security groups)", "Select existing security group", this will be your SSH, ALB and Webserver SG. By adding the SSH SG to the setup server, we will be able to SSH into it. Adding the ALB SG will allow us to access the WordPress site from the internet, adding the Webserver SG will enable us to access the RDS database. You can now create the instance, wait a few minutes to make sure that your instance is fully deployed and has the "2/2 checks passed" in the "Status check".

<img width="792" alt="241338527-c98c263a-d070-474f-96f2-386969ffda4a" src="https://github.com/leorickli/wordpress-aws/assets/106999054/be54d369-e758-45bb-8bed-50117e4705e8">
