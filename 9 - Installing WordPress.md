## Installing Wordpress

Now that we enabled SSH into our server, we are going to run a list of commands to install the WordPress site and move the files to EFS

### 1. Create the HTML directory and mount the EFS to it

We have to first update the mount information of EFS, to do that, go to the AWS console and search for EFS. On the EFS page, select your EFS on "File system ID", then click "Attach". This is where you will find the information to mount your EFS, copy only the section that is on the red circle and replace it on the \<yourEfsMountHere>\.

<img width="1475" alt="Screenshot 2023-05-27 at 13 08 26" src="https://github.com/leorickli/wordpress-aws/assets/106999054/df467bed-b989-4075-ba02-675f4ed14ef7">

```
sudo su
yum update -y
mkdir -p /var/www/html
sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport <yourEfsMountHere>:/ /var/www/html
```
- **sudo su**: This command elevates the user's privileges to superuser (root) level. It allows running subsequent commands with administrative permissions.
- **yum update -y**: This command updates all installed packages on the EC2 instance to their latest versions. The -y flag automatically answers "yes" to any prompts during the update process.
- **mkdir -p /var/www/html**: This command creates a directory named /var/www/html if it doesn't already exist. This directory is commonly used as the web document root for serving HTML files.
- **sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-003311850d60e7616.efs.us-east-1.amazonaws.com:/ /var/www/html**: This command mounts the Amazon EFS filesystem to the /var/www/html directory using the NFS (Network File System) protocol. It specifies the NFS version 4.1 and sets various options for optimal performance and reliability.
  - **nfsvers=4.1**: Specifies the NFS version to be used as 4.1.
  - **rsize=1048576**: Sets the read buffer size to 1 MB.
  - **wsize=1048576**: Sets the write buffer size to 1 MB.
  - **hard**: Configures the mount to use a hard mount type, which retries indefinitely in case of network or server issues.
  - **timeo=600**: Sets the timeout value for NFS operations to 600 seconds (10 minutes).
  - **retrans=2**: Sets the number of retransmissions to 2, meaning the client will retry failed operations twice.
  - **noresvport**: Allows the NFS client to use any available port for communication.
  - **fs-003311850d60e7616.efs.us-east-1.amazonaws.com:/**: Specifies the DNS name of the EFS filesystem and the root directory (/) to mount.

Once the code has been inserted, confirm that your EFS is mounted by typing "df - h".

<img width="576" alt="Screenshot 2023-05-27 at 21 01 48" src="https://github.com/leorickli/wordpress-aws/assets/106999054/4d05491d-8dc3-43bf-90df-05bdd8fd4b4b">

### 2. Install the Apache webserver
```
sudo yum install -y httpd httpd-tools mod_ssl
sudo systemctl enable httpd 
sudo systemctl start httpd
```

### 3. Install PHP 7.4
```
sudo amazon-linux-extras enable php7.4
sudo yum clean metadata
sudo yum install php php-common php-pear -y
sudo yum install php-{cgi,curl,mbstring,gd,mysqlnd,gettext,json,xml,fpm,intl,zip} -y
```

### 4. Install MySQL 5.7
```
sudo rpm -Uvh https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2022
sudo yum install mysql-community-server -y
sudo systemctl enable mysqld
sudo systemctl start mysqld
```

### 5. Set Permissions
```
sudo usermod -a -G apache ec2-user
sudo chown -R ec2-user:apache /var/www
sudo chmod 2775 /var/www && find /var/www -type d -exec sudo chmod 2775 {} \;
sudo find /var/www -type f -exec sudo chmod 0664 {} \;
chown apache:apache -R /var/www/html
```

### 6. Download WordPress files
```
wget https://wordpress.org/latest.tar.gz
tar -xzf latest.tar.gz
cp -r wordpress/* /var/www/html/
```

### 7. Create the wp-config.php file
```
cp /var/www/html/wp-config-sample.php /var/www/html/wp-config.php
```

### 8. Edit the wp-config.php file

This will open the Nano text editor.
```
nano /var/www/html/wp-config.php
```

Once Nano has been opened, we are going to enter the information of our RDS database. Keep pressing the down arrow until you come to the "DB_NAME", this is where you replace it with the name of your database. To retrieve your database name, go to your AWS console and search for RDS. On the RDS dashboard, select "Databases", click on your "dev-rds-db" database. You can see your "DB name" on the "Configuration" tab, in our case, it's going to be "applicationdb".

<img width="1165" alt="Screenshot 2023-05-27 at 21 26 39" src="https://github.com/leorickli/wordpress-aws/assets/106999054/8d8a2b2d-ba08-40c3-8036-9a6e9ff28dea">

We will have to put our username and password on the correct fields. Finally, we have to change our database hostname, by changing the "localhost", to do that, go to the "Connectivity & security" tab, there we will find the endpoint value that we have to paste.

<img width="1175" alt="Screenshot 2023-05-27 at 21 35 56" src="https://github.com/leorickli/wordpress-aws/assets/106999054/ea03651d-9a03-4529-a963-18c84a07d487">

<img width="673" alt="Screenshot 2023-05-27 at 21 38 02" src="https://github.com/leorickli/wordpress-aws/assets/106999054/9a211a2d-c1ac-4631-bb13-68c31b425168">

Once we finished on filling the info required, we have exit Nano, to do that, press "Control + X" > "Y" > "Enter"

### 9. Restart the webserver
```
service httpd restart
```










