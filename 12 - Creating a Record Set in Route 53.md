## Creating a Record Set in Route 53

In this section, we will create a record set in Route 53 se we can access our website using our domain name. To create it, go to the AWS console and search for Route 53. On the Route 53 dashboard, select "Hosted zones" and click on your domain name, on the "Records" page that has been opened, click "Create record".

![Screenshot 2023-05-28 at 17 45 18](https://github.com/leorickli/wordpress-aws/assets/106999054/f1c80fea-7fc3-4be9-a79e-8d7b9cf23166)

On "Record name", type "www" for your subdomain name, the record type is going to be A (IPv4), on the "Alias", toogle it on and select "Alias to application and Classic Load Balancer", we then choose the region that we have our application load balancer deployed (us-east-1), once the correct region was selected, we can select the ALB that we created.

![Screenshot 2023-05-28 at 17 48 55](https://github.com/leorickli/wordpress-aws/assets/106999054/9968c1ad-c9ac-4a40-a05a-26c0934c275a)

Now we can access our website using our domain name. To do that, just select the record we just created, copy the record name and paste it in your browser. In a previous 
