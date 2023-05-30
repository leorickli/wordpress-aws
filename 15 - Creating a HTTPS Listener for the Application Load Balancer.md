## Creating a HTTPS Listener for the Application Load Balancer

In this section, we will create a HTTPS listener for our ALB to secure our website. We already created an HTTP listener but now we will create one with the extra SSL/TLS (Secure Sockets Layer/Transport Layer Security) encryption.

### Creating the HTTPS Listener

To do that, search for EC2 in the AWS console. On the EC2 dashboard, go to "Load Balancing" > "Load Balancers", select the ALB that you created on section #10 and go for the "Listeners" tab > "Add listener". Now we will change the "Protocol" to "HTTPS" (443), on "Default actions", select "Forward" (determines how incoming requests are processed and forwarded to the target group).

<img width="808" alt="241744801-76a324bc-efd2-4518-aee9-5cc45127e338" src="https://github.com/leorickli/wordpress-aws/assets/106999054/9b9752df-c7e0-4460-9b6d-afc681b32231">

On "Secure listener settings", select the certificate that we created in section #13. Click "Add".

<img width="807" alt="241745037-751ac264-e7b7-4dcf-ab3c-85bbc898bff4" src="https://github.com/leorickli/wordpress-aws/assets/106999054/cd73888c-676e-421c-8292-df5237cc4d24">

Now we have to edit our HTTP listener to redirect HTTP traffic to HTTPS. To do that, select the "HTTP: 80" on "Listener ID" and click "Actions" > "Edit listener". On the edit page, click "Remove" on the "Default actions" and then select "Redirect", type 443 in front of the HTTPS protocol. Save changes.

<img width="813" alt="241787288-33c2a47b-cd59-447f-b6a3-9d3301e0fd40" src="https://github.com/leorickli/wordpress-aws/assets/106999054/753249b0-4b19-4da7-a064-469e9775eb9f">

### Updating the "wp-config" File

Now, to make sure that our website is secure, the next thing we need to do is to connect via Session Manager into one of our webservers in the private app subnets so we can modify the "wp-config" file. To do that, select "EC2 dashboard" > "Instances (running)". Select one of your EC2 instances, right-click on it and choose "Connect". In the "Connect to instance" page, select the "Session manager" tab and click "Connect".

<img width="815" alt="241817664-3caf8095-0eae-4e12-a032-c4e036787022" src="https://github.com/leorickli/wordpress-aws/assets/106999054/1d60ff2b-5cb6-4b5b-ad41-925ed5e0cb61">

Once connected via session manager, we will insert a series of commands, the first one will make you the root user:

```
sudo su
```

The second one will make you enter "wp-config" via the Nano text editor:

```
nano /var/www/html/wp-config.php
```

<img width="621" alt="241817849-b21df145-cee9-487f-bf0f-5e64c9181aef" src="https://github.com/leorickli/wordpress-aws/assets/106999054/66ac4cca-f262-4b99-bf38-664a54efcf83">

In the Nano text editor, keep pressing the down arrow until you you reach this point:

<img width="727" alt="241817999-3446d6e4-d454-44ec-9ccf-032f5a5a0b8e" src="https://github.com/leorickli/wordpress-aws/assets/106999054/b322bdad-98b0-43b0-922a-d001e9fbbf78">

Give a space up and down between the cursor and type the code below:

```
/* SSL Settings */
define('FORCE_SSL_ADMIN', true);

// Get true SSL status from AWS load balancer
if(isset($_SERVER['HTTP_X_FORWARDED_PROTO']) && $_SERVER['HTTP_X_FORWARDED_PROTO'] === 'https') {
  $_SERVER['HTTPS'] = '1';
}
```

- **define('FORCE_SSL_ADMIN', true)**: It sets the constant FORCE_SSL_ADMIN to true. This constant is typically used in WordPress to force the administration area (admin dashboard) to use SSL (Secure Socket Layer) for secure communication.
- The second block of code checks if the HTTP_X_FORWARDED_PROTO header is present in the request and if its value is 'https'. This header is commonly used when the application is behind a load balancer or proxy that terminates SSL connections and forwards requests to the application server. If the condition is true, it means that the request came through a secure connection. In this case, the code sets the HTTPS server variable to '1', indicating that the request was made using SSL. This is necessary because some server environments may not automatically set the HTTPS variable when using load balancers or proxies. By setting the HTTPS variable explicitly, the application can correctly determine that the request was made over HTTPS and handle it accordingly.

<img width="887" alt="241818494-2c8b536b-5f14-4acf-bba0-b54132827ed0" src="https://github.com/leorickli/wordpress-aws/assets/106999054/6785ba0a-ad55-473d-b126-3471449f4067">

No need to edit the second EC2 instance because the "wp-config" file is stored in EFS. 

### Connecting to the Website Using SSL Certificate

Now that we edited the "wp-config" file by adding the code above, we will now connect to our website to make sure that it now has an SSL connection. To do that, type your website domain with "https://".

![241819359-2aa76d6e-019e-4084-a995-43fed41ba92b](https://github.com/leorickli/wordpress-aws/assets/106999054/f11c73c1-6dfb-46a2-aac1-68aee3ab0fe6)

We can see the little lock beside our website, this means that we made a secure connection to our website! The next thing we will do is update the URL settings for our WordPress site. To do that, type "/wp-admin" in front of your domain name and press Enter. Login into the website again and in the dashboard, select "Settings" > "General". Update the "WordPress address (URL)" and "Site address (URL)" with "https" so we can have a secure connection. Save changes.

![241819712-00db1071-2afa-4d1d-af4a-385fed0d5817](https://github.com/leorickli/wordpress-aws/assets/106999054/8744591b-df47-45e9-b50f-23efe2e759e9)
