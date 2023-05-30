## Creating an Application Load Balancer and the Private Instances

![241581923-1801d7a0-6165-427a-8364-b354db49e253](https://github.com/leorickli/wordpress-aws/assets/106999054/ac68040d-7497-487e-b7d6-d8d5c3e6717c)

*An AWS Application Load Balancer (ALB) is a managed load balancing service provided by Amazon Web Services (AWS). It operates at the application layer (Layer 7) of the OSI model and is designed to distribute incoming traffic across multiple targets such as Amazon EC2 instances, containers, IP addresses, or Lambda functions.*

In this section, we will create an ALB (Application Load Balancer) to route traffic between the EC2 instances in the private app subnets. Before we create the ALB, we need to first launch the EC2 instances in their respective application subnets. We have created the Setup Server instance to have the WordPress files saved in EFS, now we can install the real instances in the private subnets, attach it to EFS and then delete the Setup Server instance in the public subnet.

### Creating the Private Instances

In the AWS console, search for EC2. On the EC2 dashboard, select "EC2 Dashboard" and then click on "Launch instance". Give it a name and choose the "Amazon Linux 2 AMI (HVM)" instance, this is important for compatibility with WordPress.

<img width="788" alt="241576689-57a08798-adba-4f33-9fdf-396394d5b621" src="https://github.com/leorickli/wordpress-aws/assets/106999054/ed4bc034-26db-447f-afb6-beb89126e3b2">
<img width="786" alt="241576762-a233ff13-e1c8-4fad-b814-6580c3a83a70" src="https://github.com/leorickli/wordpress-aws/assets/106999054/0669b444-2060-4ec1-b12a-6f0fec6074c6">

Make sure that your "Instance type" is in the free tier mode, like the "t2.micro" that is chosen by default. Then, select the "myec2key" key pair that we created.

<img width="787" alt="241576774-0b9713cc-36bb-44d0-b2fc-845576542448" src="https://github.com/leorickli/wordpress-aws/assets/106999054/f6f2432d-abe3-4cc4-83d2-069aa939128b">

For the "Network settings", choose our custom Dev VPC that we created, launch the instance on "Private App Subnet AZ1". Click on "Select existing security group" and choose the "Webserver SG".

<img width="788" alt="241576834-3d25c60c-4161-4dcc-b1ba-72d8509869da" src="https://github.com/leorickli/wordpress-aws/assets/106999054/b4fcd85d-700f-47a2-bb10-4654ac10f3f0">

For the last and most important step, go to "Advanced details" to find the "User data" section. There will be a blank field, we can paste the code below in this field, but before we do that, we have to paste our EFS mount on \<yourEfsMountHere>\. This process is called "bootstrapping", you create a set of commands that are executed after the instance has been created, it's like installing the drivers and apps after you install the OS on your machine.

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

<img width="588" alt="241577127-8be7bd12-5ac3-4adb-ad91-665e2b93bc40" src="https://github.com/leorickli/wordpress-aws/assets/106999054/416f0382-49fa-479c-98ed-1769b71f8c20">

We will now create the same instance but this time it will be the "Webserver AZ2" and it will be deployed in "Private App Subnet AZ2". Once created, we will have three EC2 instances operating.

![241579190-4edc5d3b-4340-466e-a4bf-78765c007932](https://github.com/leorickli/wordpress-aws/assets/106999054/ef542f9f-3b95-4be6-903c-a3eae263dfb7)

### Creating a Target Group

Next, we will create a target group and put these two EC2 instances in the target group to allow the ALB to route traffic to them. To create a target group, go to the EC2 dashboard and search for "Load Balancing", select "Target groups" and click "Create target group". For "Choose a target type", leave it at default on "Instances", give it a name on "Target group name" and elect our custom Dev VPC.

![241579379-be22ca82-e5f6-4d83-b7a2-fdc995951e6e](https://github.com/leorickli/wordpress-aws/assets/106999054/1d7df06b-622f-4900-b22a-75e0ede95c37)

On "Health checks", click on "Advanced health checks settings" and find the "Success codes" field. There will be a 200 in it, update the values to increment the 301 and 301 success code in it. We can click "Next".

![241579585-4d878615-d5f1-44a2-902e-1bdf3489b93e](https://github.com/leorickli/wordpress-aws/assets/106999054/624b9893-d3e3-4808-a3b1-fbd87bce9139)

HTTP success codes are used to indicate that a request has been successfully processed by a server. Here are the meanings of some commonly encountered HTTP success codes:

- **200 OK**: This status code indicates that the request was successful, and the server has returned the requested resource. It is the most common success code and indicates a successful response to a GET, POST, or PUT request.
- **301 Moved Permanently**: This status code is a redirection response. It indicates that the requested resource has been permanently moved to a new location. The client should update its bookmarks or links to the new URL provided in the response's Location header.
- **302 Found/Temporary Redirect**: This status code is also a redirection response. It indicates that the requested resource is temporarily located at a different URL. The client should typically continue to request the original URL in future requests, but for this specific request, it should follow the URL provided in the response's Location header.

On the "Register targets" page, we will select the instances we want to ALB to route to, we will choose the Webserver AZ1 and AZ2, once included, click on "Include as pending below". Click "Create target group".

![241579759-ec4a7139-46ff-4ec6-9b87-8a2b29ae65d5](https://github.com/leorickli/wordpress-aws/assets/106999054/16a9dae0-3c12-4ab8-bc2f-8d9c5bd13c35)

### Creating the Application Load Balancer

On the EC2 dashboard and on "Load Balancing" again, click on "Load Balancers", then click "Create load balancer". We will choose the Application Load Balancer (ALB) because it's more secure and we don't need the extra speed of the network load balancer.

![241580043-778cd4eb-f856-4d41-b6fa-43c43d8c8650](https://github.com/leorickli/wordpress-aws/assets/106999054/e031cbf1-fd90-4ac2-8aa0-d41eea6cad14)

Give it a name and make sure that it is "Internet-facing" and using "IPv4".

![241580181-236d1a02-947e-4e4b-85dd-5a33e37f6b7b](https://github.com/leorickli/wordpress-aws/assets/106999054/98e301ec-0725-4f4b-8f75-d62b06e2109e)

On "Network mapping", select our custom Dev VPC and on "Mappings", we have to select at least one public subnet in each AZ for the ALB.

![241580285-e77da6b7-d59e-4d20-a6cb-45919a903e3e](https://github.com/leorickli/wordpress-aws/assets/106999054/13263f49-b540-4c65-b3e5-4b72ea39dd83)

On "Security groups", we remove the default one and insert the "ALB SG". On "Listeners and routing", we will add a listener for HTTP on "Dev-TG". We can now create our elastic load balancer.

![241580437-944a4db9-d6f9-4368-8886-54180392fa83](https://github.com/leorickli/wordpress-aws/assets/106999054/398e9603-83f0-49ea-85f2-007ef4a78c3d)

Wait for the provisioning of the ALB and once done, we will use the "DNS name" of the ALB to access our website. Copy the DNS name and paste it in your browser, we should be able to see the WordPress website again.

![241580647-4eefaeda-1d35-4ff6-9390-e7d3412210f7](https://github.com/leorickli/wordpress-aws/assets/106999054/500870c9-ba6f-4787-93f8-2daf7808a502)

But there is one more thing that we need to do. Anytime we change our domain address, we have to go to the WordPress settings and change the domain address there as well. To change the domain settings in the WorPpress configuration file, insert "/wp-admin" in front of your DNS name.

![241580914-2d90bcd7-2531-4b1f-a90e-29c2dcf89cef](https://github.com/leorickli/wordpress-aws/assets/106999054/6579ca2e-4ce1-48d8-976d-0ff2f40be08e)

Next, login into your website. Once logged into the WordPress website, on the dashboard, select "Settings" > "General". We are going to update the "WordPress Address (URL)" and the "Site Address (URL)". Insert "http://" and in front of it, insert the DNS name that you copied, save changes. After saving, login again to WordPress.

![241581380-71706132-8cb5-41bc-b894-c369b856bc29](https://github.com/leorickli/wordpress-aws/assets/106999054/26f9fa7d-1b37-4f05-9d50-7ec0379bc37e)

Now we can terminate our Setup Server, to do that, go the AWS console and search for EC2. On the dashboard, select "EC2 Dashboard" > "Instances (running)". Click with your right mouse button on the Setup Sever instance and choose "Terminate instance".

![241581641-cdceabec-2823-4899-935d-e5fbed613ff6](https://github.com/leorickli/wordpress-aws/assets/106999054/3e3d02c9-8c80-4a84-9ed3-05fad2132084)
