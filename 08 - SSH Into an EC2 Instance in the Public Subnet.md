## SSH Into an EC2 Instance in the Public Subnet

In this section, we will learn how to SSH into an EC2 instance in the public subnet. There is a slight difference in installing it for Mac/Linux and Windows users.

In the EC2 dashboard, select "Instances" and then select the instance you created. Copy the public IPv4 address provided.

<img width="860" alt="Screenshot 2023-05-26 at 20 32 46" src="https://github.com/leorickli/wordpress-aws/assets/106999054/2322cdbf-91de-4961-94c1-d084d649a754">

### For Mac/Linux Users

Open your terminal and type "ssh -i myec2key.pem ec2-user@34.229.253.79". Paste your IPv4 address after "ec2-user@". Remember to store your key pair on your home directory, otherwise you will have to specify the directory that the key pair is stored in the command. For the first time using this command, you will get a warning message and it will be asking if you are sure you want to continue connecting, just type "yes" and you will be fine. Be also aware that, if by any means you change your private IP (continue with your project in a different location, like your friend's house), the connection will not be possible, you will then have to select your new IP on the SSH SG.

<img width="579" alt="Screenshot 2023-05-28 at 09 20 59" src="https://github.com/leorickli/wordpress-aws/assets/106999054/78db32a7-4159-4c25-8a70-5c14a3226394">

### For Windows Users

Open PuTTY, select "Session" on its dashboard, the "Host Name (or IP address)" will be "ec2-user@<yourIPaddress>".
  
![Captura de tela 2023-05-27 115957](https://github.com/leorickli/wordpress-aws/assets/106999054/90f6790e-2fff-4ed1-b128-0bbafc1be29e)

On the dashboard again, select the "+" on "SSH", then select the "+" on the "Auth", select "Credentials".

![Captura de tela 2023-05-27 115721](https://github.com/leorickli/wordpress-aws/assets/106999054/b1b00a45-6d1a-4696-91fc-20ef7f461f07)

The first time you access your EC2 instance, you will get a "PuTTY Security Alert", just accept it. Now you have your instance open on Windows via SSH.

<img width="894" alt="Screenshot 2023-05-27 at 12 04 20" src="https://github.com/leorickli/wordpress-aws/assets/106999054/88c614a8-cd63-4b2d-9401-cd0322acd45f">
