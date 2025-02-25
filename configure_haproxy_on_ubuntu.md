# Configure HAProxy on your Ubuntu instance for use as an SFTP server

### Step 1: Update Your System
First, ensure your system is up to date. Open a terminal and run:

```bash
sudo apt update
sudo apt upgrade -y
```

### Step 2: Install HAProxy
Install HAProxy using the package manager:

```bash
sudo apt install haproxy -y
```

### Step 3: Configure HAProxy
You need to configure HAProxy to handle SFTP traffic. Open the HAProxy configuration file:

```bash
sudo nano /etc/haproxy/haproxy.cfg
```

Add the following configuration to the file. This example assumes you have multiple SFTP servers running on different IPs or ports. Adjust the backend servers as necessary.

```haproxy
global
    log /dev/log local0
    log /dev/log local1 notice
    chroot /var/lib/haproxy
    stats socket /run/haproxy/admin.sock mode 660 level admin
    stats timeout 30s
    user haproxy
    group haproxy
    daemon

defaults
    log global
    mode tcp
    option tcplog
    timeout connect 5000ms
    timeout client 50000ms
    timeout server 50000ms

frontend sftp_frontend
    bind *:22
    default_backend sftp_backend

backend sftp_backend
    balance roundrobin
    server sftp1 <SFTP_SERVER_1_IP>:22 check
    server sftp2 <SFTP_SERVER_2_IP>:22 check
```

Replace `<SFTP_SERVER_1_IP>` and `<SFTP_SERVER_2_IP>` with the actual IP addresses of your SFTP servers. You can add more servers as needed.

Basic Example:
```bash
vi GOKUDRGBL-sftp.cfg
```
```haproxy
listen frontend_GOKUDRGBL_sftp
        bind *:2225
        timeout client 60s
        mode tcp
        option tcplog
        server GOKUDRGBL 88.202.130.64:22
```
To start HAProxy with specific configuration
```bash
haproxy -D -f GOKUDRGBL-sftp.cfg
```
- `haproxy`: This is the command to run the HAProxy application.
- `-D:` This option tells HAProxy to run in "daemon" mode. When HAProxy runs in daemon mode, it runs in the background as a service. This is useful for production environments where you want HAProxy to continuously run and manage traffic without being tied to a terminal session.
- `-f` GOKUDRGBL-sftp.cfg: The -f option specifies the configuration file that HAProxy should use. In this case, GOKUDRGBL-sftp.cfg is the name of the configuration file that contains the settings for HAProxy, including frontend and backend definitions, logging options, and other parameters.

If you want to run HAProxy with a specific configuration file for testing or development purposes, you might use this command to see how it behaves with that configuration.

### Step 4: Enable HAProxy
After configuring HAProxy, enable it to start on boot and start the service:

```bash
sudo systemctl enable haproxy
sudo systemctl start haproxy
```

### Step 5: Check HAProxy Status
Verify that HAProxy is running correctly:

```bash
sudo systemctl status haproxy
```

### Step 6: Configure Firewall (if applicable)
If you have a firewall enabled (like UFW), allow traffic on port 22:

```bash
sudo ufw allow 22
```

### Step 7: Test the Configuration
You can test the HAProxy setup by connecting to the IP address of your HAProxy server using an SFTP client. It should route the connection to one of the backend SFTP servers.

### Step 8: Monitor HAProxy (Optional)
You can enable the HAProxy stats page for monitoring. Add the following to your `haproxy.cfg`:

```haproxy
frontend stats
    bind *:8404
    stats enable
    stats uri /
    stats auth YourUsername:YourPassword
```

Replace `YourUsername` and `YourPassword` with your desired credentials. You can access the stats page by navigating to `http://<HAProxy_IP>:8404` in your web browser.

### Conclusion
You have successfully installed and configured HAProxy on your Ubuntu instance to act as a load balancer for your SFTP servers. Make sure to test the setup thoroughly to ensure it meets your needs.
