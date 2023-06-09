## SSH Into an EC2 Instance in the Public Subnet

In this section, we will learn how to SSH into an EC2 instance in the public subnet. There is a slight difference in installing it for Mac/Linux and Windows users.

In the EC2 dashboard, select "Instances" and then select the instance you created. Copy the public IPv4 address provided.

<img width="860" alt="241340267-2322cdbf-91de-4961-94c1-d084d649a754" src="https://github.com/leorickli/wordpress-aws/assets/106999054/99ea270c-d2e5-44fe-bfb9-2c3e0866bbd8">

### For Mac/Linux Users

Open your terminal and type "ssh -i myec2key.pem ec2-user@<yourIpv4Here>". Paste your IPv4 address after "ec2-user@", on \<yourIpv4Here\>. Remember to store your key pair on your home directory, otherwise you will have to specify the directory that the key pair is stored in the command. For the first time using this command, you will get a warning message and it will be asking if you are sure you want to continue connecting, just type "yes" and you will be fine. Be also aware that, if by any means you change your private IP (continue with your project in a different location, like your friend's house), the connection will not be possible, you will then have to select your new IP on the SSH SG.

<img width="579" alt="241559491-78db32a7-4159-4c25-8a70-5c14a3226394" src="https://github.com/leorickli/wordpress-aws/assets/106999054/393534a1-894e-4ba5-8871-6856d0e50d54">

### For Windows Users

Open PuTTY, select "Session" on its dashboard, the "Host Name (or IP address)" will be "ec2-user@<yourIPaddress>".

![241403715-b1b00a45-6d1a-4696-91fc-20ef7f461f07](https://github.com/leorickli/wordpress-aws/assets/106999054/b238f303-1fc4-44d8-acd6-a5f34b3f0dde)

On the dashboard again, select the "+" on "SSH", then select the "+" on the "Auth", select "Credentials".

![241403704-90f6790e-2fff-4ed1-b128-0bbafc1be29e](https://github.com/leorickli/wordpress-aws/assets/106999054/fb03906b-1be0-4947-97f7-e1aee2a7b27f)

The first time you access your EC2 instance, you will get a "PuTTY Security Alert", just accept it. Now you have your instance open on Windows via SSH.

<img width="894" alt="241403786-88c614a8-cd63-4b2d-9401-cd0322acd45f" src="https://github.com/leorickli/wordpress-aws/assets/106999054/5e81a3d5-363a-4c04-9a52-bbf87a8390fa">
