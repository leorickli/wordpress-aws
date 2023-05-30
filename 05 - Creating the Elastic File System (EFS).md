## Creating the Elastic File System (EFS)

![Screenshot 2023-05-26 at 17 01 23](https://github.com/leorickli/wordpress-aws/assets/106999054/70fae0a1-ab2a-47a6-9b79-0a51dff9ae2b)

*Amazon Elastic File System (Amazon EFS) is a fully managed, scalable, and highly available file storage service provided by Amazon Web Services (AWS). It is designed to provide shared file storage for EC2 instances and other AWS services.*

In this section, we will create an Elastic File System (EFS) with mount targets in the private data subnets in each AZ. EFS will allow the webserver and the private app subnets to pull the application code and configuration files from the same location. With Amazon EFS, multiple Amazon EC2 instances running WordPress can share the same file system. This allows you to scale your WordPress deployment horizontally by adding more EC2 instances without worrying about file synchronization or data consistency issues. All instances can access and update the same files, ensuring that changes are immediately visible across the entire deployment.

### Creating EFS

Search for "EFS" in the AWS console, then "Create file system". A window will be shown in your browser, select "Customize". Give it a name for your file system, on the "Encryption", check the "Enable encryption of data at rest" unchecked so we don't get surprised by any bills. On the "Tags" field, fill "Name" in the "Tag key" and "Dev-EFS" in the "Tag value". You can click "Next".

![Screenshot 2023-05-26 at 17 19 52](https://github.com/leorickli/wordpress-aws/assets/106999054/a27184a4-ce15-410d-a6a1-6212994aafa4)
![Screenshot 2023-05-26 at 17 20 06](https://github.com/leorickli/wordpress-aws/assets/106999054/2d3da742-2e53-4511-92e9-d1a4be7bb158)

Select your Dev VPC on the network. On "Mount targets", we will select the subnets in each AZ that we want to put our mount target in, since we are using two AZs (us-east-1a and 1b), we will use the private data subnet AZ1 and AZ2, on "Security groups", remove the default SG and select the EFS SG for both AZs.

![Screenshot 2023-05-26 at 17 24 25](https://github.com/leorickli/wordpress-aws/assets/106999054/48242edc-15e9-4659-8aba-86cd07d2f139)

No need to make changes to the "File system policy", click "Next". On the "Review and create" you can click "Create".
