## Creating an Auto Scaling Group

![Screenshot 2023-05-29 at 21 39 36](https://github.com/leorickli/wordpress-aws/assets/106999054/532c2456-1129-4aaa-9214-f824ad63dbd9)

*AWS Auto Scaling Groups (ASG) are a feature of Amazon Web Services (AWS) that allows you to automatically scale your EC2 instances based on predefined policies and conditions. ASGs are commonly used to maintain the desired number of instances in an application's fleet while automatically adjusting the capacity to meet changing demand.*

In this section, we will create an auto scaling group to dynamically create and scale the webservers in the private app subnets. To do that, we will first terminate our existing EC2 instances in the private app subnets, there is no need to keep them operating since we have all the important files in EFS and data on RDS, we will use an ASG to manage the creation and termination of instances based on our conditions

### Creating the Launch Template

First we will delete the EC2 instances. In the AWS console, search for EC2, in the EC2 dashboard, select "EC2 Dashbord" and then "Instances (running)". Right-click on both of your EC2 instances and click on "Terminate".

![Screenshot 2023-05-29 at 21 43 06](https://github.com/leorickli/wordpress-aws/assets/106999054/770de56a-0fd5-49bf-98b8-bc8918ee2af2)

Now we create a launch template, it contains the configuration of the EC2 instances that ASG will use to launch the new EC2 instances. To do that, on the EC2 dashboard, on "Instances", select "Launch Templates" > "Create launch template". Give it a name, the description can be anything, check the box that says "Provide guidance to help me set up a template that I can use with EC2 Auto Scaling".

![Screenshot 2023-05-29 at 21 50 27](https://github.com/leorickli/wordpress-aws/assets/106999054/eb584a29-ff2a-4844-8d7a-70abd068005a)

On "Applications and OS images", select "Amazon Linux" > "Amazon Linux 2 AMI (HVM)", on "Instance type", select "t2.micro" (the free one) and on "Key pair (login)", select the key pair that we created. This is just because we still have that key but we don't actually have to use it since we are using Session Manager to have access to our instances.

![Screenshot 2023-05-29 at 21 52 34](https://github.com/leorickli/wordpress-aws/assets/106999054/1c1126ab-12cc-4422-acb0-1e469fe8bc40)
![Screenshot 2023-05-29 at 21 52 54](https://github.com/leorickli/wordpress-aws/assets/106999054/d49621eb-2304-4fa0-8f2f-fe802ca673b9)

On "Network settings", keep "Select existing security groups" highlighted and select the "Web SG".

![Screenshot 2023-05-29 at 21 56 38](https://github.com/leorickli/wordpress-aws/assets/106999054/f99d10d1-6cd6-4ac2-9666-768a9277359e)

Click on "Advanced details" and scroll all the way down to the "User data" section. We will do exactly the same thing we have done in section #10, we paste the code below on the blank "User data" field but we update the <yourEfsMountHere> with our EFS mount. Click "Create launch template".

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

![Screenshot 2023-05-29 at 22 05 08](https://github.com/leorickli/wordpress-aws/assets/106999054/932ecac8-2d46-4849-aed7-1dc1614fdac4)

### Creating the Auto Scaling Group

Now we will create our ASG. To do that, find "Auto Scaling" and select "Auto Scaling Groups", click "Create Auto Scaling Groups". Give it a name and select the launch template that we just created. Click "Next".
  
![Screenshot 2023-05-29 at 22 10 43](https://github.com/leorickli/wordpress-aws/assets/106999054/e58fedd3-e28b-47a8-996f-0687ee53553f)
![Screenshot 2023-05-29 at 22 11 56](https://github.com/leorickli/wordpress-aws/assets/106999054/8c855d11-f4a1-414a-ad8c-f034df518485)

On "Network", select our custom Dev VPC and for the AZs, select our private app subnets AZ1 and AZ2. Click "Next".
  
![Screenshot 2023-05-29 at 22 14 34](https://github.com/leorickli/wordpress-aws/assets/106999054/80cf959b-235d-4a58-96eb-17f8feb56a1a)

On "Load balancing", we will "Attach to an existing load balancer" and choose our "Dev-TG" target group. On "Health checks", we will "Turn on Elastic Load Balancing health checks" and on "Additional settings" we will "Enable group metrics collection within CloudWatch". Click "Next".
  
![Screenshot 2023-05-29 at 22 18 57](https://github.com/leorickli/wordpress-aws/assets/106999054/14bc3267-bc4f-4e44-9675-3936872032)
![Screenshot 2023-05-29 at 22 19 17](https://github.com/leorickli/wordpress-aws/assets/106999054/3e5af466-5577-440f-90c4-67c3b65dea30)

On "Group size", we will set our desired capacity to 2,1,4. This way, ASG will try to maintain 2 instances deployed, the minimum will be 1 when there is minimal or zero demand and the maximum will be 4 when there is max demand. Click "Next".
  
![Screenshot 2023-05-29 at 22 22 20](https://github.com/leorickli/wordpress-aws/assets/106999054/7fad8d2d-359c-4012-bc65-083f953fbce

On "Add notifications", click "Add notification" > "Create a topic". Give it a name and select a random email address. Click "Next".
  
![Screenshot 2023-05-29 at 22 28 08](https://github.com/leorickli/wordpress-aws/assets/106999054/629a324e-1505-4160-916d-7d3ea7b00616)
  
On "Add tags", click "Add tag". On "Key", fill it with "Name" and on "Value", fill it with "ASG-Webserver" or something related.
  
![Screenshot 2023-05-29 at 22 43 57](https://github.com/leorickli/wordpress-aws/assets/106999054/9d047dfe-97d5-4259-8c04-0135d5342c31)

On the next page you can review all the info that you provided, once reviews, click on "Create auto scaling group".
  
  
  
  
  
  
