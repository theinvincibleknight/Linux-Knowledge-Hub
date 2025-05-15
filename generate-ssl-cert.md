## Step-by-step documentation to generate and assign a new SSL certificate for the `contracts.company.com` URL:

1. Install Certbot: A tool for automatically obtaining and renewing SSL/TLS certificates from Let's Encrypt.

```bash
sudo apt install certbot
```
2. Configure any pending packages: Run `dpkg --configure -a` to configure any packages that were left in an unconfigured state during the installation process.

```bash
sudo dpkg --configure -a
```

3. Check if any process is listening on port 80: Use `netstat` to check if any process was listening on port 80, which is the default HTTP port. This step helps ensure that there are no conflicts when obtaining the SSL certificate.

```bash
sudo netstat -alpnt | grep :80
```

4. Stop the web server processes: If you are working with the web server configuration, stop the Nginx and Apache2 services to avoid any conflicts.

```bash
sudo service nginx stop
sudo service apache2 stop
```

5. Obtain the SSL certificate: Use Certbot to obtain the SSL certificate for `contracts.company.com` using the `certonly` command. This command will prompt you to enter an email address and accept the terms of service.

```bash
sudo certbot certonly -d contracts.company.com
```
6. Check Nginx configuration: Open the Nginx configuration file (`/etc/nginx/nginx.conf`) using the `vi` editor to check for existing SSL certificate path.

```bash
sudo vi /etc/nginx/nginx.conf
```

7. Search for configuration files: Use `grep` to search for occurrences of "company" and "cert" in the Nginx configuration files. This step can be helpful to identify relevant configuration files that might need to be updated. (If needed)
```bash
sudo grep -r company /etc/nginx/
sudo grep -ri cert /etc/nginx/
```
8. Back up existing certificates: Create a backup of the existing certificates directory (`/volody/Certificates/`) by copying it to `old_volody_Certificates`.

```bash
sudo cp -r /volody/Certificates/ old_volody_Certificates
```
9. Check the new certificate files: List the contents of the `/etc/letsencrypt/live/` and `/etc/letsencrypt/live/contracts.company.com` directories to verify the presence of the newly obtained SSL certificate files.

```bash
ls /etc/letsencrypt/live/
sudo ls /etc/letsencrypt/live/contracts.company.com
```
10. Copy the new certificate files: Copy the `fullchain.pem` (certificate chain) and `privkey.pem` (private key) files from the letsencrypt directory to the `/volody/Certificates/` directory, which is likely the location where your web server expects the SSL certificate files.

```bash
sudo cp /etc/letsencrypt/live/contracts.company.com/fullchain.pem /volody/Certificates/bundle_fullchain.pem
sudo cp /etc/letsencrypt/live/contracts.company.com/privkey.pem /volody/Certificates/private.pem
```
11. Start the web server processes: After updating the SSL certificate files, start the Nginx and Apache2 services to apply the changes and serve the website with the new SSL certificate.

```bash
sudo service nginx start
sudo service apache2 start
```

### Additional Steps:

To configure automatic renewal of the SSL certificate obtained through Let's Encrypt and Certbot, you can follow these steps:

1. Open the Certbot renewal configuration file:
```bash
sudo nano /etc/letsencrypt/renewal/contracts.company.com.conf
```
This file contains the configuration settings for renewing the SSL certificate for `contracts.company.com`.

2. Check the renewal settings:
Ensure that the following lines are present and uncommented in the configuration file:

```bash
renew_before_expiry = 30 days
authenticate_by_certonly = true
```
- `renew_before_expiry` specifies the number of days before the certificate expiration when Certbot should attempt to renew the certificate.
- `authenticate_by_certonly` tells Certbot to use the certonly method for renewal, which is the recommended way for web servers like Apache or Nginx.


3. Save and exit the configuration file.

4. Test the renewal process:
You can test the renewal process by running the following command:
```bash
sudo certbot renew --dry-run
```
This command simulates the renewal process without actually obtaining a new certificate. It should output information about the certificates that are due for renewal and the actions that would be taken.

5. Configure a cron job for automatic renewal:
To set up automatic renewal, you need to create a cron job that runs the `certbot renew` command periodically. Open the crontab editor with the following command:
```bash
sudo crontab -e
```
Add the following line to the crontab file:
```bash
0 3 * * * /usr/bin/certbot renew --quiet
```
This cron job will run the `certbot renew` command at 3:00 AM every day. The `--quiet` flag suppresses the output unless there are errors or important messages.
Save and exit the crontab file.

6. Verify the cron job:
You can verify that the cron job has been added correctly by running the following command:

```bash
sudo crontab -l
```
This command will list all the cron jobs for the root user, and you should see the entry you just added.

7. Configure email notifications (optional):
If you want to receive email notifications about the certificate renewal process, you can configure Certbot to send emails. Open the Certbot configuration file:
```bash
sudo nano /etc/letsencrypt/renewal/contracts.company.com.conf
```
Uncomment the following line and replace `your_email@example.com` with your email address:
```bash
email = your_email@example.com
```
Save and exit the configuration file.

That's it! With these steps, Certbot will automatically attempt to renew the SSL certificate for `contracts.company.com` every day at 3:00 AM. If the renewal is successful, the new certificate will be automatically installed and configured for your web server.

Note that you may need to adjust the cron job schedule and email settings based on your specific requirements and preferences. Additionally, ensure that your web server configuration is set up to use the renewed certificates automatically.

---

## Extras

For certificate renewal, we need to open port 80 in the inbound rules of the Security Group attached to the instance. This is necessary during the renewal process, and once the activation is complete, we will need to remove that rule. We can use the following bash script on the same instance and set a cron job to run this script every month for renewal.

```bash
#!/bin/bash

SECURITY_GROUP_ID="sg-0652edba917fd87d7"
INSTANCE_ID="i-034990c6d56d84ad0"
REGION="ap-south-1"

# Function to add port 80 to the security group
add_port_80() {
    aws ec2 authorize-security-group-ingress --group-id $SECURITY_GROUP_ID --protocol tcp --port 80 --cidr 0.0.0.0/0 --region $REGION
}

# Function to remove port 80 from the security group
remove_port_80() {
    aws ec2 revoke-security-group-ingress --group-id $SECURITY_GROUP_ID --protocol tcp --port 80 --cidr 0.0.0.0/0 --region $REGION
}

# Adding port 80 to the security group
add_port_80

sleep 10

# Stop Apache, renew the certificate, and start Apache
sudo systemctl stop apache2
sudo certbot renew
sleep 5
sudo systemctl start apache2

# Check if the certificate was renewed successfully
if sudo certbot certificates | grep -q "VALID"; then
    echo "Certificate renewed successfully."
else
    echo "Certificate renewal failed."
fi

sleep 10

# Remove port 80 from the security group
remove_port_80
```

