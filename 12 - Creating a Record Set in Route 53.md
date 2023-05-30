## Creating a Record Set in Route 53

In this section, we will create a record set in Route 53 so we can access our website using our domain name. To create it, go to the AWS console and search for Route 53. On the Route 53 dashboard, select "Hosted zones" and click on your domain name, on the "Records" page that has been opened, click "Create record".

![241586473-f1c80fea-7fc3-4be9-a79e-8d7b9cf23166](https://github.com/leorickli/wordpress-aws/assets/106999054/3a1092f8-90b1-4a18-a19b-27dbd9e83b18)

On "Record name", type "www" for your subdomain name, the record type is going to be A (IPv4). On "Alias", toggle it on and select "Alias to application and Classic Load Balancer", we then choose the region that we have our application load balancer deployed (us-east-1), once the correct region was selected, we can select the ALB that we created.

![241586834-9968c1ad-c9ac-4a40-a05a-26c0934c275a](https://github.com/leorickli/wordpress-aws/assets/106999054/bff3246e-ea94-49ab-81fd-e7cf1f64bcd4)

Now we can access our website using our domain name. To do that, just select the record we just created, copy the record name and paste it in your browser. In section #10, every time that the domain is changed, you would have to go into your WordPress settings and change the domain there too, to fix that, paste your domain name again in the browser and type "/wp-admin" in front of it.

![241587236-1d8caaa1-aa72-4e62-a898-999314d66abc](https://github.com/leorickli/wordpress-aws/assets/106999054/b9991c5a-ee23-4c4c-9532-915a60e65929)

Login to the website again. On the dashboard, select "Settings" > "General", paste the A record name that you created on Route 53 on "WordPress Address (URL)" and "Site Address (URL)" with "http://" in the beginning. Save changes.

![241587650-82069f5f-5067-4261-9661-9a14fd717c48](https://github.com/leorickli/wordpress-aws/assets/106999054/1e51b0a1-1cf7-4981-b876-c8e8b91913bf)

Login again, once logged in, we will see that the website has your domain name now.
