## Creating a Key Pair

In this section, we will create a key pair that we will use to SSH into our EC2 instance. In the AWS console, search for "EC2". On the EC2 dashboard, select "Key Pairs" then "Create key pair". Give it a name and select ".pem" for Mac/Linux users and ".ppk" for Windows users. 

![Screenshot 2023-05-26 at 17 53 26](https://github.com/leorickli/wordpress-aws/assets/106999054/33cb578c-dc9f-462f-a298-4ef9132c9281)

When you create a key pair, two keys will be generated for you. The first key is called a public and the second is called a private key. The key that you see in the AWS management console is the public key, this is the key that you will put on your EC2 instance when you launch it. The key that it's been downloaded to your computer is the private key. So anytime you need to SSH into your EC2 instance, you will put the public key on that EC2 instance, then you will use the private key that was downloaded into your computer to SSH into that EC2 instance.

### For Mac/Linux Users

Select the ".pem" format for the key when creating it. Make sure to move the key that was downloaded to your home folder. Open up your terminal and type "chmod 400 myec2key.pem" to enable the permissions for the key.

![Screenshot 2023-05-26 at 18 15 19](https://github.com/leorickli/wordpress-aws/assets/106999054/7ec4e4ee-90ef-46be-b9a3-3369e0aad246)

### For Windows Users

Select the ".ppk" format for the key when creating it. We will also have to download [PuTTY](https://www.putty.org/) to make the connection with the EC2 instance.
