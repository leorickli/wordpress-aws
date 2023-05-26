## Creating the RDS Instance

![Screenshot 2023-05-26 at 15 36 13](https://github.com/leorickli/wordpress-aws/assets/106999054/d936fb71-fa49-4deb-ab36-0dbaecf786fe)

In this section, we will create the RDS database in the private data subnets. The main database will be in AZ1 and the standby replica will be in the AZ1, making it a Multi-AZ instance.

### Subnet Groups

Search for RDS in the AWS console. Before we create the RDS instance in the private data subnets, the first thing we need to create is subnet groups, this allows us to specify in which subnets we want to create our RDS database. To do that, on the RDS dashboard, select "Subnet groups", then "Create DB subnet group", give it a name and this same name as its description, select your custom Dev VPC. On "Add subnets", in the "Availability Zones", select the AZs that we want to put use for the subnet groups (us-east-1a and 1b), on "Subnets", we will select the subnets according to our architecture diagram above (10.0.4.0/24 and 10.0.5.0/24).

![Screenshot 2023-05-26 at 15 46 12](https://github.com/leorickli/wordpress-aws/assets/106999054/6cac8c13-03e2-47c0-881b-620f2ab524ed)
![Screenshot 2023-05-26 at 15 46 29](https://github.com/leorickli/wordpress-aws/assets/106999054/66eb2a50-95fa-44ce-8612-684ee9b5d81a)

### Creating the Database

Now we can create the database, in the RDS dashboard, select "Databases", then "Create database". On "Choose a database creation method", choose "Standard create" so we can insert detailed information for its creation, and of course, learn more from it. On "Engine options", select "MySQL" and for the "Version", we have to select a compatible version for Wordpress, select the latest 5.7.xx version of MySQL. For "Templates", select "Dev/Test" and in "Availability and durability", select "Multi-AZ DB instance", this is important because this option allow us to create a standby replica, the free tier can't do that. Of course, you can use the free tier if you want but be aware that the standby replica will not be available for this option. The presence of a standby replica is not paramount to the fun

![Screenshot 2023-05-26 at 16 12 03](https://github.com/leorickli/wordpress-aws/assets/106999054/37db1fe8-c42b-4987-afe4-faf1732aa7d0)
![Screenshot 2023-05-26 at 16 12 23](https://github.com/leorickli/wordpress-aws/assets/106999054/ab6bc3e8-2af7-4a5d-bb65-2a9e9579d1bb)

