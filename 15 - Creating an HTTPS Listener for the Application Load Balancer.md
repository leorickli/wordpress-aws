## Creating an HTTPS Listener for the Application Load Balancer

In this section, we will create an HTTPS listener for our ALB to secure our website. We already created an HTTP listener but now we will create one with the extra SSL/TLS (Secure Sockets Layer/Transport Layer Security) encryption.

To do that, search for EC2 in the AWS console. On the EC2 dashboard, go to "Load Balancing" > "Load Balancers", select the ALB that you created on section #10 and go for the "Listeners" tab > "Add listener". Now we will change the "Protocol" to "HTTPS" (443), on "Default actions", select "Forward" (determines how incoming requests are processed and forwarded to the target group).

<img width="808" alt="Screenshot 2023-05-29 at 10 43 42" src="https://github.com/leorickli/wordpress-aws/assets/106999054/76a324bc-efd2-4518-aee9-5cc45127e338">

On "Secure listener settings", select the certificate that we created on section #13. Click "Add".

<img width="807" alt="Screenshot 2023-05-29 at 10 45 02" src="https://github.com/leorickli/wordpress-aws/assets/106999054/751ac264-e7b7-4dcf-ab3c-85bbc898bff4">

Now we have to edit our HTTP listener to redirect HTTP traffic to HTTPS. To do that, select the "HTTP: 80" on "Listener ID" and click "Actions" > "Edit listener". On the edit page, click "Remove" on the "Default actions" and then select "Redirect", type 443 in front of the HTTPS protocol. Save changes.

<img width="813" alt="Screenshot 2023-05-29 at 15 13 48" src="https://github.com/leorickli/wordpress-aws/assets/106999054/33c2a47b-cd59-447f-b6a3-9d3301e0fd40">
