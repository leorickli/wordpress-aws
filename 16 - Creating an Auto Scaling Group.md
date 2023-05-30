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

Click on "Advanced details" and scroll all the way down to the "User data" section

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
echo "fs-03c9b3354880b36a6.efs.us-east-1.amazonaws.com:/ /var/www/html nfs4 nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2 0 0" >> /etc/fstab
mount -a
chown apache:apache -R /var/www/html
sudo service httpd restart
```

### Creating the Auto Scaling Group
