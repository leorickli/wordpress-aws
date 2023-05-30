## Registering for an SSL Certificate in AWS Certificate Manager.md

*An SSL certificate is a digital certificate that authenticates the identity of a website and enables secure encrypted communication between the website and its visitors. It ensures that the data transmitted between the user's browser and the website's server remains confidential and cannot be intercepted or tampered with by unauthorized parties, this is also known as encryption in transit.*

In this section, we will register for a free SSL certificate from the AWS Certificate Manager and we will use the SSL certificate to encrypt all communications between the web browser and our webservers.

To do that, search for Certificate Manager in the AWS console. In the Certificate Manager console, select "Request a certificate", on the "Request certificate" page, keep with the option to "Request a public certificate" and then click "Next".

![Screenshot 2023-05-28 at 18 11 30](https://github.com/leorickli/wordpress-aws/assets/106999054/e1cbf6a9-1cf1-4288-9bd2-31dc2cbed64b)

Enter your domain name, once you have entered your domain name, click on "Add another name to this certificate" to add a wildcard for your domain name. The wildcard allows you to have some other options for your domain name like a "www". For that, type "\*." and then your domain name. Click "Request".

![Screenshot 2023-05-28 at 18 19 36](https://github.com/leorickli/wordpress-aws/assets/106999054/e3cd6a76-c2d3-4d9e-b091-a5def8160a28)

To view it, click on "View certificate" on the top-right section of your screen. You will see the "Certificate status" page and a "Pending validation" status, it's still pending because we have to create a record set in Route 53 to validate that the digital domain name belongs to us. To do that, click "Create records in Route 53".

<img width="1121" alt="Screenshot 2023-05-28 at 21 32 19" src="https://github.com/leorickli/wordpress-aws/assets/106999054/563160fe-25db-4826-ab2b-f5031453c4de">

On the "Create DNS records in Amazon Route 53", make sure you have checked your domain name and wildcard, click "Create records".

<img width="1090" alt="Screenshot 2023-05-28 at 21 33 56" src="https://github.com/leorickli/wordpress-aws/assets/106999054/0a1e5ec5-1d8e-4f5f-8960-23a9ae48eed1">

Wait for the status to be on "Success" and we are done.

<img width="1123" alt="Screenshot 2023-05-28 at 21 43 03" src="https://github.com/leorickli/wordpress-aws/assets/106999054/a716b0c3-cbc1-4054-8a7b-b9c8185aa4a2">
