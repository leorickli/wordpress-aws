## Registering a New Domain Name in Route 53

*AWS Route 53 is a scalable and highly available domain name system (DNS) web service provided by Amazon Web Services (AWS). It offers domain registration, DNS routing, and health checking of resources.*

In this section, we will register a new domain name in Route 53. This will allow our end users to access the website using a domain name instead of the DNS name of our ALB. To register a new domain name, search for Route 53 in the AWS console. Now we are in the Route 53 dashboard, first we have to check if the domain name that we want is available, put a random name that you want on it and click "Check"

![Screenshot 2023-05-28 at 16 41 53](https://github.com/leorickli/wordpress-aws/assets/106999054/1a9080bf-e6de-420b-938e-2e471ff426d8)
![Screenshot 2023-05-28 at 16 43 07](https://github.com/leorickli/wordpress-aws/assets/106999054/19dc58e6-b3ca-446e-b435-38e4c599bae3)

We can see that this domain name is available, unfortunately, this section will incur some costs, there is no free domain name that you can choose. You can choose the ".click" domain that is much cheaper comapred to the ".com". Click "Continue". Fill your personal info in the next page and click "Next". On the last page, it will ask if you want to automaticall renew your domain after one year, it's better to disable it for this project, agree with the terms and conditions and click "Complete Order".

To check for the progress of your domain name registration, go to the dashboard and click on "Pending requests". Wait a few minutes for it to be completed.

![Screenshot 2023-05-28 at 17 14 27](https://github.com/leorickli/wordpress-aws/assets/106999054/43465ea5-1519-4579-8d14-ab444777203e)

