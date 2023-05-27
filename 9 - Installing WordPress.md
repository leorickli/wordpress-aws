## Installing Wordpress

Now that we enabled SSH into our server, we are going to run a list of commands to install the WordPress site and move the files to EFS

1. Create the HTML directory and mount the EFS to it. We have to first update the mount information of EFS, to do that, go to the AWS console and search for EFS. On the EFS page, select your EFS on "File system ID", then click "Attach". This is where you will find the information to mount your EFS, copy only the section that is on the red circle and replace it on the \<yourEfsMountHere>\.

<img width="1475" alt="Screenshot 2023-05-27 at 13 08 26" src="https://github.com/leorickli/wordpress-aws/assets/106999054/df467bed-b989-4075-ba02-675f4ed14ef7">

```
sudo su
yum update -y
mkdir -p /var/www/html
sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport <yourEfsMountHere>:/ /var/www/html
```


2. Install Apache
```
sudo yum install -y httpd httpd-tools mod_ssl
sudo systemctl enable httpd 
sudo systemctl start httpd
```

3. Install PHP 7.4
```
sudo amazon-linux-extras enable php7.4
sudo yum clean metadata
sudo yum install php php-common php-pear -y
sudo yum install php-{cgi,curl,mbstring,gd,mysqlnd,gettext,json,xml,fpm,intl,zip} -y
```

4. Install MySQL 5.7
```
sudo rpm -Uvh https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2022
sudo yum install mysql-community-server -y
sudo systemctl enable mysqld
sudo systemctl start mysqld
```

5. Set Permissions
```
sudo usermod -a -G apache ec2-user
sudo chown -R ec2-user:apache /var/www
sudo chmod 2775 /var/www && find /var/www -type d -exec sudo chmod 2775 {} \;
sudo find /var/www -type f -exec sudo chmod 0664 {} \;
chown apache:apache -R /var/www/html
```

6. Download WordPress files
```
wget https://wordpress.org/latest.tar.gz
tar -xzf latest.tar.gz
cp -r wordpress/* /var/www/html/
```

7. Create the wp-config.php file
```
cp /var/www/html/wp-config-sample.php /var/www/html/wp-config.php
```

8. Edit the wp-config.php file
```
nano /var/www/html/wp-config.php
```

9. Restart the webserver
```
service httpd restart
```










