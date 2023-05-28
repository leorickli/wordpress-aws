## Creating an Application Load Balancer

In this section, we will create and ALB (Application Load Balancer) to route traffic between the EC2 instances in the private app subnets. Before we create the ALB, we need to first lauch the EC2 instances in their respective application subnets. We have created the Setup Server instance to have the Wordpress files save in EFS, now we can install the real instances in the private subnets, attach it to EFS and then delete the Setup Server instance in the public subnet.

In the AWS console, search for EC2. On the EC2 dashboard, select "EC2 Dashboard" and then click on "Launch instance". Give it a name and choose the "Amazon Linux 2 AMI (HVM)" instance, this is important for compatibility with Wordpress.

<img width="788" alt="Screenshot 2023-05-28 at 14 31 44" src="https://github.com/leorickli/wordpress-aws/assets/106999054/57a08798-adba-4f33-9fdf-396394d5b621">
<img width="786" alt="Screenshot 2023-05-28 at 14 33 13" src="https://github.com/leorickli/wordpress-aws/assets/106999054/a233ff13-e1c8-4fad-b814-6580c3a83a70">

Make sure that your "Instance type" is in the free tier mode, like the "t2.micro" that is chosen by default. Then, select the "myec2key" key pair that we created.

<img width="787" alt="Screenshot 2023-05-28 at 14 33 36" src="https://github.com/leorickli/wordpress-aws/assets/106999054/0b9713cc-36bb-44d0-b2fc-845576542448">

For the "Network settings", choose our custom Dev VPC that we created, launch the instance on "Private App Subnet AZ1". Click on "Select existing security group" and choose the "Webserver SG"

<img width="788" alt="Screenshot 2023-05-28 at 14 35 08" src="https://github.com/leorickli/wordpress-aws/assets/106999054/3d25c60c-4161-4dcc-b1ba-72d8509869da">

For the last and most important step, go to "Advanced details" to find the "User data" section. There

<img width="588" alt="Screenshot 2023-05-28 at 14 40 42" src="https://github.com/leorickli/wordpress-aws/assets/106999054/8be7bd12-5ac3-4adb-ad91-665e2b93bc40">
