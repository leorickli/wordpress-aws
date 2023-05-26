## Launching the Setup Server

<img width="820" alt="Screenshot 2023-05-26 at 20 24 54" src="https://github.com/leorickli/wordpress-aws/assets/106999054/327fbbda-e889-450b-a129-1fdec8d70556">

I this section, we will launch the EC2 istance that will install Wordpress in the public subnet AZ1.

In the AWS console, search for EC2. On the EC2 dashboard, in the "Resources" tab, select "Instances (running)" then "Launch Instances". Give it a name, one the "Application and OS Images (Amazon Machine Images), select "Amazon Linux 2023AMI", this is the free tier AMI.

<img width="790" alt="Screenshot 2023-05-26 at 20 01 15" src="https://github.com/leorickli/wordpress-aws/assets/106999054/a9a23e21-adc7-422e-aab2-f77202edb90d">

On "Instance type", make sure that "t2.micro" is selected to keep up with the free tier option. On the "Key pair", select the key pair that you created.

<img width="787" alt="Screenshot 2023-05-26 at 20 03 15" src="https://github.com/leorickli/wordpress-aws/assets/106999054/2a0f0311-0827-48d2-9bda-c01d45873ba1">

On "Network settings", click "Edit". Select your custom Dev VPC and for the subnet, select the public subnet AZ1, this is where we will deploy our EC2 instance, on Firewalls (security groups)", "Select existing security group", this will be your SSH, ALB and Webserver SG. By adding the SSH SG to the setup server, we will be able to SSH into it. Adding the ALB SG will allow us to access the WordPress site from the internet, adding the Webserver SG will enable us to access the RDS database. You can now create the instance, wait a few minutes to make sure that you instance is fully deployd and has the "2/2 checks passed" in the "Status check".

<img width="792" alt="Screenshot 2023-05-26 at 20 08 14" src="https://github.com/leorickli/wordpress-aws/assets/106999054/c98c263a-d070-474f-96f2-386969ffda4a">
