## Creating the Elastic File System (EFS)

![241316178-70fae0a1-ab2a-47a6-9b79-0a51dff9ae2b](https://github.com/leorickli/wordpress-aws/assets/106999054/623a7c77-b760-4f7c-8389-5ede7e09a8f7)

*Amazon Elastic File System (Amazon EFS) is a fully managed, scalable, and highly available file storage service provided by Amazon Web Services (AWS). It is designed to provide shared file storage for EC2 instances and other AWS services.*

In this section, we will create an Elastic File System (EFS) with mount targets in the private data subnets in each AZ. EFS will allow the webserver and the private app subnets to pull the application code and configuration files from the same location. With Amazon EFS, multiple Amazon EC2 instances running WordPress can share the same file system. This allows you to scale your WordPress deployment horizontally by adding more EC2 instances without worrying about file synchronization or data consistency issues. All instances can access and update the same files, ensuring that changes are immediately visible across the entire deployment.

### Creating EFS

Search for "EFS" in the AWS console, then "Create file system". A window will be shown in your browser, select "Customize". Give it a name for your file system, on the "Encryption", check the "Enable encryption of data at rest" unchecked so we don't get surprised by any bills. On the "Tags" field, fill "Name" in the "Tag key" and "Dev-EFS" in the "Tag value". You can click "Next".

![241318566-a27184a4-ce15-410d-a6a1-6212994aafa4](https://github.com/leorickli/wordpress-aws/assets/106999054/b2e9816f-89c2-42d8-957f-b6f2aaee161e)
![241318602-2d3da742-2e53-4511-92e9-d1a4be7bb158](https://github.com/leorickli/wordpress-aws/assets/106999054/cfac451b-4dd4-483f-b41a-e8956b2b3676)

Select your Dev VPC on the network. On "Mount targets", we will select the subnets in each AZ that we want to put our mount target in, since we are using two AZs (us-east-1a and 1b), we will use the private data subnet AZ1 and AZ2, on "Security groups", remove the default SG and select the EFS SG for both AZs.

![241319272-48242edc-15e9-4659-8aba-86cd07d2f139](https://github.com/leorickli/wordpress-aws/assets/106999054/1ad6d6bb-25a3-4539-b517-b85c863d2bfd)

No need to make changes to the "File system policy", click "Next". On the "Review and create" you can click "Create".
