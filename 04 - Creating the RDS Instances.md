## Creating the RDS Instances

![241297747-d936fb71-fa49-4deb-ab36-0dbaecf786fe](https://github.com/leorickli/wordpress-aws/assets/106999054/9a70a891-2491-4555-b62b-d61fc35cd8c6)

*Amazon RDS (Relational Database Service) is a managed database service provided by AWS. It offers a scalable and cost-effective solution for deploying and managing relational databases in the cloud. By implementing Amazon RDS in a WordPress deployment on AWS, you can leverage a scalable, managed, and highly available database service. It simplifies database administration, improves performance and scalability, enhances security, and integrates seamlessly with other AWS services, ultimately providing a reliable and efficient backend for your WordPress site.*

In this section, we will create the RDS database in the private data subnets. The main database will be in AZ2 and the standby replica will be in AZ1, making it a Multi-AZ instance.

### Subnet Groups

Search for RDS in the AWS console. Before we create the RDS instance in the private data subnets, the first thing we need to create is subnet groups, this allows us to specify in which subnets we want to create our RDS database. To do that, on the RDS dashboard, select "Subnet groups", then "Create DB subnet group", give it a name and this same name as its description, select your custom Dev VPC. On "Add subnets", in the "Availability Zones", select the AZs that we want to use for the subnet groups (us-east-1a and 1b), on "Subnets", we will select the subnets according to our architecture diagram above (10.0.4.0/24 and 10.0.5.0/24).

![241299467-6cac8c13-03e2-47c0-881b-620f2ab524ed](https://github.com/leorickli/wordpress-aws/assets/106999054/0c82c8d6-8891-4e1b-8dfc-e9b34c1a1072)
![241299501-66eb2a50-95fa-44ce-8612-684ee9b5d81a](https://github.com/leorickli/wordpress-aws/assets/106999054/4502c843-39f6-415f-97c0-058d6855a373)

### Creating the Database

Now we can create the database, in the RDS dashboard, select "Databases", then "Create database". On "Choose a database creation method", choose "Standard create" so we can insert detailed information for its creation, and of course, learn more from it. On "Engine options", select "MySQL" and for the "Version", we have to select a compatible version for WordPress, select the latest 5.7.xx version of MySQL. For "Templates", select "Dev/Test" and in "Availability and durability", select "Multi-AZ DB instance", this is important because this option allows us to create a standby replica, the free tier can't do that. Of course, you can use the free tier if you want but be aware that the standby replica will not be available for this option. The presence of a standby replica is not paramount to the execution of this project but it's nice to implement it for a HA environment.

![241303973-37db1fe8-c42b-4987-afe4-faf1732aa7d0](https://github.com/leorickli/wordpress-aws/assets/106999054/95005fbc-de99-4622-ac4d-a14550772770)
![241304027-ab6bc3e8-2af7-4a5d-bb65-2a9e9579d1bb](https://github.com/leorickli/wordpress-aws/assets/106999054/496d8d77-0e08-489d-a017-5ee0effa7e13)
![241305782-e0258828-48fc-434d-bce1-c47c0dd8e50e](https://github.com/leorickli/wordpress-aws/assets/106999054/201be10e-8d35-4500-b65d-0c67ee0ccc8b)

Proceeding with the creation, on "Settings", give your DB a name, a master username and a password. On "DB instance class", select the burstable classes and toogle "Include previous generation classes", AWS will give you the free instance. On "Connectivity", select your custom Dev VPC, make sure that you have your subnet groups selected for the "Subnet group" field, for the "VPC security group", make sure that you "Choose existing" SG, the existing one that we will use is the Database SG, delete the "default" option, on "Availability Zone", select "us-east-1b" according to the diagram above.

![241308892-f4b0ca78-e386-416b-b9eb-377e403196b9](https://github.com/leorickli/wordpress-aws/assets/106999054/325c1903-2188-467b-a8e5-c715ad64f0de)
![241309271-7cdaaf46-fb3c-44ee-afc6-915ecbb4b0d1](https://github.com/leorickli/wordpress-aws/assets/106999054/7dfb5eb2-8f8a-4959-ba63-e2dafbe2e480)
![241310497-51e461fb-da87-4d70-8964-d0f0db1a937a](https://github.com/leorickli/wordpress-aws/assets/106999054/a177622e-ca73-4426-8fbc-cfeb5b472d43)
![241312230-7c8852ad-ab57-4841-84fb-b0243f15a061](https://github.com/leorickli/wordpress-aws/assets/106999054/155c895c-b524-4fc6-8053-e8de9d8a8559)

Finally, on "Additional configuration", enter your database name on "Initial database name".

![241313200-56a5e699-c477-44b8-9882-61383ab7c3cc](https://github.com/leorickli/wordpress-aws/assets/106999054/261ed03c-e75d-4189-8884-1b6bfd2b18a3)

You can now create your database, wait a few minutes for it to be complete. Make sure you save your credentials by clicking on "View credential details".
