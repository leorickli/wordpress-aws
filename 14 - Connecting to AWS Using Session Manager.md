## Connecting to AWS Using Session Manager

In this section, we will be using the AWS Systems Manager Session Manager to make a connection to our private EC2 instances. You could deploy a Bastion Host which is just an EC2 instance in the public subnet that has access to the private EC2 instances, but it's more efficient to use Session Manager because you don't need to use key pairs and SSH SG, you can delegate access to manage EC2 instances using IAM roles and of course, you don't need to create another EC2 instance for that. 

We are going to create an IAM role for the EC2 instance so we assume a role to access the private EC2 instances. To do that, go to your AWS console and search for IAM. On the IAM dashboard, select "Roles" and then "Create role". On "Use case", select "EC2" and click next.

<img width="1087" alt="241611537-544eee7a-b175-48e7-8d59-c915391540a1" src="https://github.com/leorickli/wordpress-aws/assets/106999054/ee0afffd-67c5-486e-94fb-b6a2ec73debb">

Now we will find the right permission policy for this situation, we will filter it by the name "AmazonSSMManagedInstanceCore".

<img width="1088" alt="241611812-d9bb7f03-f080-47d5-8360-a2e80fcb2737" src="https://github.com/leorickli/wordpress-aws/assets/106999054/ddb10f06-fd5b-42c4-be01-09ff7d780bfe">

Give it a name, the description can remain the same or you can make your own.

<img width="812" alt="241612111-d6aab098-046b-4512-afd2-9c707044e8d5" src="https://github.com/leorickli/wordpress-aws/assets/106999054/44fc68d4-603b-40bf-80cd-2b8c4996d751">

Now let's go back to our EC2 instances and attach this new role to them. To do that, search for EC2 in the AWS console. On the EC2 dashboard, click on "EC2 dashboard" and then click on "Instances running". Select one of your EC2 instances, on the "Actions" menu, select "Security" > "Modify IAM role". Insert the IAM role that you just created in the blank field.

<img width="812" alt="241613133-62381660-696f-4e76-b16a-dbc99aacb04d" src="https://github.com/leorickli/wordpress-aws/assets/106999054/a6cc83d6-fa31-4222-b919-cf74de3f2041">

This can take some time; it took nearly 40 minutes for me. Don't forget to do that on your second EC2 instance.

<img width="810" alt="241619437-f6d7bad8-acad-486e-b8ab-9d2ef46393c0" src="https://github.com/leorickli/wordpress-aws/assets/106999054/95991c24-5e4a-4ca1-a489-9a5fcb4c009d">
