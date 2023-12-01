# Install Apache ActiveMQ on Ubuntu 20.04

Apache ActiveMQ is a free, open-source, and Java-based message broker software from Apache Foundation. It is used to send messages between different applications and offers features like STOMP, JMS, and OpenWire. It supports industry-standard protocols that allow communication between separate applications.

The Apache ActiveMQ is the best choice for you if you are looking for a high-performance message broker for your applications.

## Prerequisites
- A server running Ubuntu 20.04.
- A root password is configured the server.

## Install Java

Apache ActiveMQ is a Java-based application so Java must be installed in your server. If not installed, you can install it by running the following command:

```bash
apt-get install openjdk-11-jdk openjdk-11-jre -y
```

Once installed, you can verify the Java version with the following command:

```bash
java -version
```

You should get the following output:

```bash
openjdk version "11.0.8" 2020-07-14
OpenJDK Runtime Environment (build 11.0.8+10-post-Ubuntu-0ubuntu120.04)
OpenJDK 64-Bit Server VM (build 11.0.8+10-post-Ubuntu-0ubuntu120.04, mixed mode, sharing)
```

## Install Apache ActiveMQ

At the time, the latest version of ActiveMQ is 5.16.0. You can download it with the following command:

```bash
wget https://downloads.apache.org//activemq/5.16.0/apache-activemq-5.16.0-bin.tar.gz
```
Once the download is completed, extract the downloaded file using the following command:

```bash
tar -xvzf apache-activemq-5.16.0-bin.tar.gz
```
Next, move the extracted directory to the /opt with the following command:

```bash
mv apache-activemq-5.16.0 /opt/activemq
```
Next, you will need to create a new user and group to run Apache ActiveMQ. You can create them with the following command:

```bash
addgroup --quiet --system activemq
adduser --quiet --system --ingroup activemq --no-create-home --disabled-password activemq
```
Next, change the ownersip of the /opt/activemq to activemq as shown below:

```bash
chown -R activemq:activemq /opt/activemq
```
Once you are finished, you can proceed to the next step.

## Create a Systemd Service File for ActiveMQ

Next, you will need to create a systemd service file to manage the ActiveMQ service. You can create it with the following command:

```bash
nano /etc/systemd/system/activemq.service
```
Add the following lines:
```bash
[Unit]
Description=Apache ActiveMQ
After=network.target
[Service]
Type=forking
User=activemq
Group=activemq

ExecStart=/opt/activemq/bin/activemq start
ExecStop=/opt/activemq/bin/activemq stop

[Install]
WantedBy=multi-user.target
```
Save and close the file when you are finished. Then, reload the systemd daemon with the following command:

```bash
systemctl daemon-reload
```
Next, start the ActiveMQ service and enable it to start at system reboot with the following command:

```bash
systemctl start activemq
systemctl enable activemq
```
You can now check the status of ActiveMQ service with the following command:

```bash
systemctl status activemq
```
You should get the following output:

```bash
? activemq.service - Apache ActiveMQ
     Loaded: loaded (/etc/systemd/system/activemq.service; disabled; vendor preset: enabled)
     Active: active (running) since Sat 2020-08-01 11:43:25 UTC; 22s ago
    Process: 31092 ExecStart=/opt/activemq/bin/activemq start (code=exited, status=0/SUCCESS)
   Main PID: 31165 (java)
      Tasks: 49 (limit: 2353)
     Memory: 190.5M
     CGroup: /system.slice/activemq.service
             ??31165 /usr/bin/java -Xms64M -Xmx1G -Djava.util.logging.config.file=logging.properties -Djava.security.auth.login.config=/opt/ac>

Aug 01 11:43:25 ubuntu2004 systemd[1]: Starting Apache ActiveMQ...
Aug 01 11:43:25 ubuntu2004 activemq[31092]: INFO: Loading '/opt/activemq//bin/env'
Aug 01 11:43:25 ubuntu2004 activemq[31092]: INFO: Using java '/usr/bin/java'
Aug 01 11:43:25 ubuntu2004 activemq[31092]: INFO: Starting - inspect logfiles specified in logging.properties and log4j.properties to get deta>
Aug 01 11:43:25 ubuntu2004 activemq[31164]: INFO: pidfile created : '/opt/activemq//data/activemq.pid' (pid '31165')
Aug 01 11:43:25 ubuntu2004 systemd[1]: Started Apache ActiveMQ.
lines 1-16/16 (END)
```
At this point, ActiveMQ is running and listening on port 8161.

## Access Apache ActiveMQ Web Interface

By default, Apache ActiveMQ is configured to access from the localhost. So you will need to configure it to access from the external hosts. You can configure it by editing `/opt/activemq/conf/jetty.xml` file:

```bash
nano /opt/activemq/conf/jetty.xml
```
Find the following line:

```bash
        <property name="host" value="127.0.0.1"/>
```
Replace it with the following line:

```bash
        <property name="host" value="your-server-ip"/>
```
Save and close the file then restart the Apache ActiveMQ service to apply the changes:

```bash
systemctl restart activemq
```

Now, open your web browser and access the Apache ActiveMQ using the URL http://your-server-ip:8161:/admin. You will be asked for a username and password.

Provide default username and password as admin / admin and click on the Sign In button. You should see the Apache ActiveMQ dashboard.

