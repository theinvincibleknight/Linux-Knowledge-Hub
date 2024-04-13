# Adding persistent static routes on Ubuntu

## Introduction

When you need to access network devices located on a different network segment than the one you are on, you need to have a route set up so the networking stack knows how to get to the other network segment. This generally just points to your main gateway, but you may want to set up additional static routes, where you don’t want the traffic going through your main default gateway.

## Adding Routes from your network to different network segments

You need to add routes to your server using the below commands.

```bash
$ sudo ip route add 20.0.140.21 dev ens5 scope link metric 100
$ sudo ip route add 151.193.250.41 via 20.0.140.21 dev ens5
```

You can confirm your routes have been added by issuing the following command:

```bash
$ ip r
```

This works fine until reboot. After the machine restarts, you need to manually add these routes again. So we need to add the persistent routes that don't get affected when the machine restarts.

With the introduction of Ubuntu 18.04, along came the netplan YAML-based network configuration tool.

The netplan configuration files are located in the `/etc/netplan` folder.

## Find the netplan config file

Run the following command to list the netplan configuration files

```bash
$ ls /etc/netplan
```

You should see something like the following:

```bash
$ ls /etc/netplan
01-netcfg.yaml
$
```

or

```bash
$ ls /etc/netplan
50-cloud-init.yaml
$
```

In this example, we will be using the `“50-cloud-init.yaml”` file

## Edit the netplan configuration file

Open up your netplan config file in a text editor.

```bash
$ sudo vi /etc/netplan/50-cloud-init.yaml
```

You will be presented with a default configuration similar to this:
(network interface names will differ from machine to machine)

```bash
# This file is generated from information provided by the datasource.  Changes
# to it will not persist across an instance reboot.  To disable cloud-init's
# network configuration capabilities, write a file
# /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg with the following:
# network: {config: disabled}
network:
    ethernets:
        eth0:
            dhcp4: true
            dhcp6: false
            match:
                macaddress: 02:0a:6d:38:77:a2
            set-name: eth0
```
Add the following lines to the bottom of the config section for the network interface you want the route to be assigned to.

```bash
        routes:
                - to: 20.0.0.0/16
                via: 172.31.32.1
                metric: 100
```

The file should now look something like this:

```bash
# This file is generated from information provided by the datasource.  Changes
# to it will not persist across an instance reboot.  To disable cloud-init's
# network configuration capabilities, write a file
# /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg with the following:
# network: {config: disabled}
network:
    ethernets:
        eth0:
            dhcp4: true
            dhcp6: false
            match:
                macaddress: 02:0a:6d:38:77:a2
            set-name: eth0
            routes:
                - to: 20.0.0.0/16
                via: 172.31.32.1
                metric: 100
```

Save the configuration, and exit the editor.

Applying the configuration change

You can either apply the changes straight away, with the following command:

```bash
$ sudo netplan apply
```

or, if you want to test it first, and potentially roll back any changes, you can use the following command:

```bash
$ sudo netplan try
```

This option applies the changes and provides a 120 timeout whereby if you don’t accept the changes, they will revert back. This is useful to prevent you from locking yourself out of the system if the network change didn’t work the way you were intending.

The message that will be displayed will look like this:

```bash
$ sudo netplan try

Do you want to keep these settings?

Press ENTER before the timeout to accept the new configuration

Changes will revert in 120 seconds
```

Simply press the `ENTER` key on the keyboard before the timeout expires, and the changes will have been applied.

## Checking the routes have been added

You can confirm your routes have been added by issuing the following command:

```bash
$ ip route show 
```

You should see your newly added route appearing in the list of routes. It should appear something like this:

```bash
default via 172.31.32.1 dev eth0 proto dhcp src 172.31.45.53 metric 100
20.0.0.0/16 via 172.31.32.1 dev eth0 proto static metric 100 onlink
172.31.0.2 via 172.31.32.1 dev eth0 proto dhcp src 172.31.45.53 metric 100
172.31.32.0/20 dev eth0 proto kernel scope link src 172.31.45.53 metric 100
172.31.32.1 dev eth0 proto dhcp scope link src 172.31.45.53 metric 100
```

Check the syslog file for network-related errors using the following command:

```bash
$ grep networkd /var/log/syslog
```

## Alternative Method

In Debian-based Linux servers, `netplan` option might not be available. Or even after adding the required routes in yaml file of netplan, still you might be facing issues with routes. In this case, you can mention the commands that need to be run manually to add routes in the bash script and run that bash script at every restart using crontab. So that routes will be added every time the machine restarts without manual interaction.

Create a bash script by adding the commands you run manually.

```bash
#!/bin/sh

ip route add 20.0.140.21 dev ens5 scope link
ip route add 151.193.250.80 via 20.0.140.21 dev ens5
ip route add 151.193.250.81 via 20.0.140.21 dev ens5
ip route add 151.193.250.82 via 20.0.140.21 dev ens5
```

Save the bash script with `.sh` extention. For example, `route_table.sh`. And provide executable permission using `chmod +x` command.

```bash
sudo chmod +x route_table.sh
```

Add an entry in crontab, To open and edit crontab use the below command.

```bash
sudo crontab -e
```

Add the following command at the end of the crontab

```bash
@reboot /path/to/script.sh
```
or

```bash
@reboot root sh /path/to/script.sh
```

Save and exit. Provide the exact path of the script in place of `/path/to/script.sh`.

Now every time the machine restarts, it runs the bash script and routes will be added automatically without any manual interaction.

You can confirm your routes have been added by issuing the following command:

```bash
$ ip route show 
```
or

```bash
$ route -n
```
or

```bash
$ netstat -r
```
