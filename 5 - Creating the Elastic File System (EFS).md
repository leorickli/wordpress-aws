## Creating the Elastic File System (EFS)

![Screenshot 2023-05-26 at 17 01 23](https://github.com/leorickli/wordpress-aws/assets/106999054/70fae0a1-ab2a-47a6-9b79-0a51dff9ae2b)

Amazon Elastic File System (Amazon EFS) is a fully managed, scalable, and highly available file storage service provided by Amazon Web Services (AWS). It is designed to provide shared file storage for EC2 instances and other AWS services.

In this section, we will create an Elastic File System (EFS) with mount targets in the private data subnets in each AZ. EFS will allow the webserver and the private app subnets to pull the application code and configuration files from the same location. With Amazon EFS, multiple Amazon EC2 instances running WordPress can share the same file system. This allows you to scale your WordPress deployment horizontally by adding more EC2 instances without worrying about file synchronization or data consistency issues. All instances can access and update the same files, ensuring that changes are immediately visible across the entire deployment.
