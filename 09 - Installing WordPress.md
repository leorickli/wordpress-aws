## Installing Wordpress

Now that we enabled SSH into our server, we are going to run a list of commands in the terminal to install the WordPress site and move the files to EFS.

### 1. Create the HTML directory and mount the EFS to it

We have to first update the mount information of EFS, to do that, go to the AWS console and search for EFS. On the EFS page, select your EFS on "File system ID", then click "Attach". This is where you will find the information to mount your EFS, copy only the section that is on the red circle and replace it on the \<yourEfsMountHere>\.

<img width="1475" alt="241409404-df467bed-b989-4075-ba02-675f4ed14ef7" src="https://github.com/leorickli/wordpress-aws/assets/106999054/b27b10f1-0ce9-448c-9978-75efffc9acbd">

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

<img width="576" alt="241453565-4d05491d-8dc3-43bf-90df-05bdd8fd4b4b" src="https://github.com/leorickli/wordpress-aws/assets/106999054/5ac0c27d-dc2c-4b25-b9b3-86da7e45aeb8">

### 2. Install the Apache webserver
```
sudo yum install -y httpd httpd-tools mod_ssl
sudo systemctl enable httpd 
sudo systemctl start httpd
```

- **sudo yum install -y httpd httpd-tools mod_ssl**: This command installs the Apache HTTP Server package (httpd), the necessary tools (httpd-tools), and the SSL module (mod_ssl) for secure connections. The sudo command is used to run the installation with administrative privileges.
- **sudo systemctl enable httpd**: This command enables the httpd service to start automatically at boot time. It sets up the appropriate system links to ensure the service starts on system startup.
- **sudo systemctl start httpd**: This command starts the httpd service immediately. It initiates the Apache HTTP Server, allowing it to handle incoming HTTP requests.

### 3. Install PHP 7.4
```
sudo amazon-linux-extras enable php7.4
sudo yum clean metadata
sudo yum install php php-common php-pear -y
sudo yum install php-{cgi,curl,mbstring,gd,mysqlnd,gettext,json,xml,fpm,intl,zip} -y
```
- **sudo amazon-linux-extras enable php7.4**: This command enables the Amazon Linux Extra repository for PHP 7.4. It allows you to install and update packages related to PHP.
- **sudo yum clean metadata**: This command cleans the local package metadata cache, ensuring that the latest package information is fetched from the enabled repositories.
- **sudo yum install php php-common php-pear -y**: This command installs PHP itself (php), along with the common PHP libraries and utilities (php-common) and PHP Extension and Application Repository (php-pear).
- **sudo yum install php-{cgi,curl,mbstring,gd,mysqlnd,gettext,json,xml,fpm,intl,zip} -y**: This command installs additional PHP extensions and modules. The extensions listed in the command include CGI (php-cgi), cURL (php-curl), Multibyte String (php-mbstring), GD Graphics Library (php-gd), MySQL Native Driver (php-mysqlnd), Gettext (php-gettext), JSON (php-json), XML (php-xml), FastCGI Process Manager (php-fpm), Internationalization (php-intl), and ZIP (php-zip).

### 4. Install MySQL 5.7
```
sudo rpm -Uvh https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2022
sudo yum install mysql-community-server -y
sudo systemctl enable mysqld
sudo systemctl start mysqld
```
- **sudo rpm -Uvh https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm**: This command downloads and installs the MySQL Community Server repository configuration package.
- **sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2022**: This command imports the GPG key used to sign the MySQL packages, ensuring their authenticity.
- **sudo yum install mysql-community-server -y**: This command installs the MySQL Community Server package.
- **sudo systemctl enable mysqld**: This command enables the MySQL service to start automatically on system boot.
- **sudo systemctl start mysqld**: This command starts the MySQL service.

### 5. Set Permissions
```
sudo usermod -a -G apache ec2-user
sudo chown -R ec2-user:apache /var/www
sudo chmod 2775 /var/www && find /var/www -type d -exec sudo chmod 2775 {} \;
sudo find /var/www -type f -exec sudo chmod 0664 {} \;
chown apache:apache -R /var/www/html
```
- **sudo usermod -a -G apache ec2-user**: This command adds the ec2-user to the apache group. By adding the user to the group, it allows the user to access files and directories owned by the apache group.
- **sudo chown -R ec2-user:apache /var/www**: This command changes the ownership of the /var/www directory and its contents to ec2-user:apache. This ensures that the ec2-user and the apache group have the necessary permissions to manage the files.
- **sudo chmod 2775 /var/www && find /var/www -type d -exec sudo chmod 2775 {} \;**: These commands set the directory permissions of /var/www and its subdirectories to 2775. The 2 at the beginning ensures that new files and directories inherit the group ownership, and the 775 permissions grant read, write, and execute permissions to the owner and group, and read and execute permissions to others.
- **sudo find /var/www -type f -exec sudo chmod 0664 {} \;**: This command sets the file permissions of /var/www and its subdirectories to 0664. The 6 at the beginning grants read and write permissions to the owner and group, and read permissions to others.
- **chown apache:apache -R /var/www/html**: This command changes the ownership of the /var/www/html directory and its contents to apache:apache. This is typically done to ensure that the web server process (running as the apache user) has the necessary permissions to read and serve the web content.

### 6. Download WordPress files
```
wget https://wordpress.org/latest.tar.gz
tar -xzf latest.tar.gz
cp -r wordpress/* /var/www/html/
```
- **wget https://wordpress.org/latest.tar.gz**: This command uses the wget tool to download the latest version of WordPress as a compressed tarball (latest.tar.gz) from the official WordPress website.
- **tar -xzf latest.tar.gz**: This command extracts the contents of the latest.tar.gz tarball using the tar command. The options -xzf specify that the tarball should be extracted (-x), the input file is compressed with gzip (-z), and the filename is provided (-f).
- **cp -r wordpress/\* /var/www/html/**: This command copies all the files and directories from the extracted wordpress directory to the document root directory of the web server (/var/www/html/). The -r option ensures that the copy operation is recursive and includes all subdirectories and files.

### 7. Create the wp-config.php file
```
cp /var/www/html/wp-config-sample.php /var/www/html/wp-config.php
```
- This command is used to make a copy of the WordPress sample configuration file (wp-config-sample.php) and rename it to wp-config.php.

### 8. Edit the wp-config.php file

This will open the Nano text editor.
```
nano /var/www/html/wp-config.php
```

Once Nano has been opened, we are going to enter the information of our RDS database. Keep pressing the down arrow until you come to the "DB_NAME", this is where you replace it with the name of your database. To retrieve your database name, go to your AWS console and search for RDS. On the RDS dashboard, select "Databases", click on your "dev-rds-db" database. You can see your "DB name" on the "Configuration" tab, in our case, it's going to be "applicationdb".

<img width="1165" alt="241455604-8d8a2b2d-ba08-40c3-8036-9a6e9ff28dea" src="https://github.com/leorickli/wordpress-aws/assets/106999054/7351b741-c8df-4649-a832-3862c7b728f5">

We will have to put our username and password on the correct fields. Finally, we have to change our database hostname, by changing the "localhost", to do that, go to the "Connectivity & security" tab, there we will find the endpoint value that we have to paste.

<img width="1175" alt="241456343-ea03651d-9a03-4529-a963-18c84a07d487" src="https://github.com/leorickli/wordpress-aws/assets/106999054/6442d26d-d9c8-4a02-a48c-6bef3ca8bcf5">
<img width="673" alt="241456493-9a211a2d-c1ac-4631-bb13-68c31b425168" src="https://github.com/leorickli/wordpress-aws/assets/106999054/28071572-cf49-45f3-ad7f-6cff18811a73">

Once we finished on filling the info required, we exit Nano. To do that, press "Control + X" > "Y" > "Enter".

### 9. Restart the webserver
```
service httpd restart
```
- This command is used to restart the Apache server.

### First login

We can now connect into our Wordpress website, just copy your instance's "Public IPv4 address" again and paste it on your browser, we will see the Wordpress welcome page and you can insert your data, then click "Install WordPress".

<img width="751" alt="241575270-07843a94-aa06-485f-aa6b-e9216c23a592" src="https://github.com/leorickli/wordpress-aws/assets/106999054/dc7d5a52-b7c2-437b-a796-731f93fa4086">

Once you've logged in to the site, we can see that finally we have our WordPress loud and pround in the screen.

<img width="1506" alt="241575447-a3701526-4196-440f-a6eb-1968fe454fc8" src="https://github.com/leorickli/wordpress-aws/assets/106999054/aedcdfd3-772c-4793-a690-3af4c382031b">
