## SSH Into an EC2 Instance in the Public Subnet

In this section, we will learn how to SSH into an EC2 instance in the public subnet.

In the EC2 dashboard, select "Instances" and then select the instance you created. Copy the public IPv4 address provided.

<img width="860" alt="Screenshot 2023-05-26 at 20 32 46" src="https://github.com/leorickli/wordpress-aws/assets/106999054/2322cdbf-91de-4961-94c1-d084d649a754">

### For Mac Users

Open your terminal and type "ssh -i myec2key.pem ec2-user@34.229.253.79". Paste your IPv4 address after "ec2-user@". Remember to store your key pair on your home directory, otherwise you will have to specify the directory that the key pair is stored in the command. For the first time using this command, you will get a warning message and it will be asking if you are sure you want to continue connecting, just type "yes" and you will be fine. Be also aware that, if by any means you change your private IP (continue with your project in a different location, like your friend's house), the connection will not be possible, you will then have to select your new IP on the SSH SG.

<img width="576" alt="Screenshot 2023-05-26 at 21 18 23" src="https://github.com/leorickli/wordpress-aws/assets/106999054/ff1b2018-c258-456f-9639-1a8824cb305a">

### For Windows Users
