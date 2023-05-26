## Creating the RDS Instance

![Screenshot 2023-05-26 at 15 36 13](https://github.com/leorickli/wordpress-aws/assets/106999054/d936fb71-fa49-4deb-ab36-0dbaecf786fe)

In this section, we will create the RDS database in the private data subnets. The main database will be in AZ1 and the standby replica will be in the AZ1.

### Subnet Groups

Search for RDS in the AWS console. Before we create the RDS instance in the private data subnets, the first thing we need to create is subnet groups, this allows us to specify in which subnets we want to create our RDS database. To do that, on the RDS dashboard, select "Subnet groups", then "Create DB subnet group", give it a name and this same name as its description, select your custom Dev VPC. On "Add subnets", in the "Availability Zones", select the AZs that we want to put use for the subnet groups (us-east-1a and 1b), on "Subnets", we will select the subnets according to our architecture diagram above (10.0.4.0/24 and 10.0.5.0/24).

![Screenshot 2023-05-26 at 15 46 12](https://github.com/leorickli/wordpress-aws/assets/106999054/6cac8c13-03e2-47c0-881b-620f2ab524ed)
![Screenshot 2023-05-26 at 15 46 29](https://github.com/leorickli/wordpress-aws/assets/106999054/66eb2a50-95fa-44ce-8612-684ee9b5d81a)

### Creating the Database

Now we can create the database, in the RDS dashboard, select "Databases", then "Create database"
