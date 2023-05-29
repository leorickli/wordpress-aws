## Creating an Application Load Balancer and the Private Instances

![Screenshot 2023-05-28 at 16 13 39](https://github.com/leorickli/wordpress-aws/assets/106999054/1801d7a0-6165-427a-8364-b354db49e253)

*An AWS Application Load Balancer (ALB) is a managed load balancing service provided by Amazon Web Services (AWS). It operates at the application layer (Layer 7) of the OSI model and is designed to distribute incoming traffic across multiple targets such as Amazon EC2 instances, containers, IP addresses, or Lambda functions.*

In this section, we will create and ALB (Application Load Balancer) to route traffic between the EC2 instances in the private app subnets. Before we create the ALB, we need to first lauch the EC2 instances in their respective application subnets. We have created the Setup Server instance to have the Wordpress files save in EFS, now we can install the real instances in the private subnets, attach it to EFS and then delete the Setup Server instance in the public subnet.

### Creating the Private Instances

In the AWS console, search for EC2. On the EC2 dashboard, select "EC2 Dashboard" and then click on "Launch instance". Give it a name and choose the "Amazon Linux 2 AMI (HVM)" instance, this is important for compatibility with Wordpress.

<img width="788" alt="Screenshot 2023-05-28 at 14 31 44" src="https://github.com/leorickli/wordpress-aws/assets/106999054/57a08798-adba-4f33-9fdf-396394d5b621">
<img width="786" alt="Screenshot 2023-05-28 at 14 33 13" src="https://github.com/leorickli/wordpress-aws/assets/106999054/a233ff13-e1c8-4fad-b814-6580c3a83a70">

Make sure that your "Instance type" is in the free tier mode, like the "t2.micro" that is chosen by default. Then, select the "myec2key" key pair that we created.

<img width="787" alt="Screenshot 2023-05-28 at 14 33 36" src="https://github.com/leorickli/wordpress-aws/assets/106999054/0b9713cc-36bb-44d0-b2fc-845576542448">

For the "Network settings", choose our custom Dev VPC that we created, launch the instance on "Private App Subnet AZ1". Click on "Select existing security group" and choose the "Webserver SG"

<img width="788" alt="Screenshot 2023-05-28 at 14 35 08" src="https://github.com/leorickli/wordpress-aws/assets/106999054/3d25c60c-4161-4dcc-b1ba-72d8509869da">

For the last and most important step, go to "Advanced details" to find the "User data" section. There will be a blank field, we can paste the code below in this field, but before we do that, we have to paste our EFS mount on \<yourEfsMountHere>\.

```
#!/bin/bash
yum update -y
sudo yum install -y httpd httpd-tools mod_ssl
sudo systemctl enable httpd 
sudo systemctl start httpd
sudo amazon-linux-extras enable php7.4
sudo yum clean metadata
sudo yum install php php-common php-pear -y
sudo yum install php-{cgi,curl,mbstring,gd,mysqlnd,gettext,json,xml,fpm,intl,zip} -y
sudo rpm -Uvh https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2022
sudo yum install mysql-community-server -y
sudo systemctl enable mysqld
sudo systemctl start mysqld
echo "<yourEfsMountHere>:/ /var/www/html nfs4 nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2 0 0" >> /etc/fstab
mount -a
chown apache:apache -R /var/www/html
sudo service httpd restart
```

<img width="588" alt="Screenshot 2023-05-28 at 14 40 42" src="https://github.com/leorickli/wordpress-aws/assets/106999054/8be7bd12-5ac3-4adb-ad91-665e2b93bc40">

We will now create the same instance but this time it will be the "Webserver AZ2" and it will be deployed in "Private App Subnet AZ2". Once created, we will have three EC2 instances operating.

![Screenshot 2023-05-28 at 15 18 57](https://github.com/leorickli/wordpress-aws/assets/106999054/4edc5d3b-4340-466e-a4bf-78765c007932)

### Creating a Target Group

Next, we will create a target group and put these two EC2 instances in the target group to allow the ALB to route traffic to them. To create a target group, go to the EC2 dashboard and search for "Load Balancing", select "Target groups" and click "Create target group". For "Choose a target type", leave it at default on "Instances", give it a name on "Target group name" and elect our custom Dev VPC.

![Screenshot 2023-05-28 at 15 22 13](https://github.com/leorickli/wordpress-aws/assets/106999054/be22ca82-e5f6-4d83-b7a2-fdc995951e6e)

On "Health checks", click on "Advanced health checks settings" and find the "Success codes" field. There will be a 200 in it, update the values to increment the 301 and 301 success code in it. We can click "Next".

![Screenshot 2023-05-28 at 15 25 22](https://github.com/leorickli/wordpress-aws/assets/106999054/4d878615-d5f1-44a2-902e-1bdf3489b93e)

HTTP success codes are used to indicate that a request has been successfully processed by a server. Here are the meanings of some commonly encountered HTTP success codes:

- **200 OK**: This status code indicates that the request was successful, and the server has returned the requested resource. It is the most common success code and indicates a successful response to a GET, POST, or PUT request.
- **301 Moved Permanently**: This status code is a redirection response. It indicates that the requested resource has been permanently moved to a new location. The client should update its bookmarks or links to the new URL provided in the response's Location header.
- **302 Found/Temporary Redirect**: This status code is also a redirection response. It indicates that the requested resource is temporarily located at a different URL. The client should typically continue to request the original URL in future requests, but for this specific request, it should follow the URL provided in the response's Location header.

On the "Register targets" page, we will select the instances we want to ALB to route to, we will choose the Webserver AZ1 and AZ2, once included, click on "Include as pending below". Click "Create target group".

![Screenshot 2023-05-28 at 15 29 33](https://github.com/leorickli/wordpress-aws/assets/106999054/ec4a7139-46ff-4ec6-9b87-8a2b29ae65d5)

### Creating the Application Load Balancer

On the EC2 dashboard and on "Load Balancing" again, click on "Load Balancers", then click "Create load balancer". We will choose the Application Load Balancer (ALB) because it's more secure and we don't need the extra speed of the network load balancer.

![Screenshot 2023-05-28 at 15 35 34](https://github.com/leorickli/wordpress-aws/assets/106999054/778cd4eb-f856-4d41-b6fa-43c43d8c8650)

Give it a name and make sure that it is "Internet-facing" and using "IPv4".

![Screenshot 2023-05-28 at 15 37 43](https://github.com/leorickli/wordpress-aws/assets/106999054/236d1a02-947e-4e4b-85dd-5a33e37f6b7b)

On "Network mapping", select our custom Dev VPC and on "Mappings", we have to select at least one public subnet in each AZ for the ALB.

![Screenshot 2023-05-28 at 15 40 09](https://github.com/leorickli/wordpress-aws/assets/106999054/e77da6b7-d59e-4d20-a6cb-45919a903e3e)

On "Security groups", we remove the default one and insert the "ALB SG". On "Listeners and routing", we will add a listener for HTTP on "Dev-TG". We can now create our elastic load balancer.

![Screenshot 2023-05-28 at 15 42 41](https://github.com/leorickli/wordpress-aws/assets/106999054/944a4db9-d6f9-4368-8886-54180392fa83)

Wait for the provisioning of the ALB and once done, we will use the "DNS name" of the ALB to access our website. Copy the DNS name and paste it in your browser, we should be able to see the WordPress website again.

![Screenshot 2023-05-28 at 15 46 45](https://github.com/leorickli/wordpress-aws/assets/106999054/4eefaeda-1d35-4ff6-9390-e7d3412210f7)

But there is one more thing that we need to do. Anytime we change your domain address, we have to go to the WordPress settings and change the domain address there as well. To change the domain settings in the Wordpress configuration file, insert "/wp-admin" in front of your DNS name.

![Screenshot 2023-05-28 at 15 51 43](https://github.com/leorickli/wordpress-aws/assets/106999054/2d90bcd7-2531-4b1f-a90e-29c2dcf89cef)

Next, login into your website. Once logged into the WordPress website, on the dashboard, select "Settings" > "General". We are going to update the "WordPress Address (URL)" and the "Site Address (URL)". Insert "http://" and in front of it, insert the DNS name that you copied, save changes. After saving, login again to WordPress.

![Screenshot 2023-05-28 at 16 00 23](https://github.com/leorickli/wordpress-aws/assets/106999054/71706132-8cb5-41bc-b894-c369b856bc29)

Now we can terminate our Setup Server, to do that, go the AWS console and search for EC2. On the dashboard, select "EC2 Dashboard" > "Instances (running)". Click with your right mouse button on the Setup Sever instance and choose "Terminate instance".

![Screenshot 2023-05-28 at 16 07 36](https://github.com/leorickli/wordpress-aws/assets/106999054/cdceabec-2823-4899-935d-e5fbed613ff6)
