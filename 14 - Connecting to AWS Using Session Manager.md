## Connecting to AWS Using Session Manager

In this section, we will be using the AWS Systems Manager Session Manager to make a connection to our private EC2 instances. You could deploy a Bastion Host which is just an EC2 instance in the public subnet that has access to the private EC2 instances, but it's more efficient to use Session Manager because you don't need to use key pairs or and SSH SG, you can delegate access to manage EC2 instances using IAM roles and, of course, you don't need to create another EC2 instance for that. 

We are going to create an IAM role for the EC2 instance, so we assume a role to access the private EC2 instances. To do that, go to your AWS console and search for IAM. On the IAM dashboard, select "Roles" and then "Create role". On "Use case", select "EC2" and click next.

<img width="1087" alt="Screenshot 2023-05-28 at 23 07 03" src="https://github.com/leorickli/wordpress-aws/assets/106999054/544eee7a-b175-48e7-8d59-c915391540a1">

Now we will find the right permission policy for this situation, we will filter it by the name "AmazonSSMManagedInstanceCore".

<img width="1088" alt="Screenshot 2023-05-28 at 23 11 32" src="https://github.com/leorickli/wordpress-aws/assets/106999054/d9bb7f03-f080-47d5-8360-a2e80fcb2737">

Give it a name, the description can remain the same or you can make your own.

<img width="812" alt="Screenshot 2023-05-28 at 23 14 15" src="https://github.com/leorickli/wordpress-aws/assets/106999054/d6aab098-046b-4512-afd2-9c707044e8d5">

Now let's go back to our EC2 instances and attach this new role to them. To do that, search for EC2 in the AWS console. On the EC2 dashboard, click on "EC2 dashboard" and then click on "Instances running". Select one of your EC2 instances, on the "Actions" menu, select "Security" > "Modify IAM role". Insert the IAM role that you just created in the blank field.

<img width="812" alt="Screenshot 2023-05-28 at 23 22 01" src="https://github.com/leorickli/wordpress-aws/assets/106999054/62381660-696f-4e76-b16a-dbc99aacb04d">

This can take 5 to 10 minutes to be completed. Don't forget to do that on your second EC2 instance.
