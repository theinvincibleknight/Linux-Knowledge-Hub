# Suricata installation
 
## Steps to install
 
- Run `apt update`
- Run `apt install suricata`
- Check the primary interface name by running `ip r show`. It will be of the format `ethXX` or `ensXX`
- Run `vi /etc/suricata/suricata.yaml`
- Search for `af-packet` (Use '/' to search for a word inside vi editor)
- Where it says `interface: eth0`, change that to the interface identified in the third step. Save and close.
- Copy the **`feodo.rules`** file to `/etc/suricata/rules`
- Run `ls -1 /etc/suricata/rules/`

You will get output similar to following output:

```bash
# ls -1 /etc/suricata/rules/
app-layer-events.rules
decoder-events.rules
dhcp-events.rules
dnp3-events.rules
dns-events.rules
feodo.rules
files.rules
http-events.rules
http2-events.rules
ipsec-events.rules
kerberos-events.rules
modbus-events.rules
mqtt-events.rules
nfs-events.rules
ntp-events.rules
smb-events.rules
smtp-events.rules
stream-events.rules
tls-events.rules
```

- Copy the output.
- Run `vi /etc/suricata/suricata.yaml`
- Go to the end of the file (Press Esc. then press Shift + G)
- Just above the end of the file, you will see

```
rule-files:
  - suricata.rules
```
- Delete the line having `suricata.rules` and then paste the contents copied above
- For each line, prefix it with '  - ' (two spaces, a hyphen and then a space) (This is how a list is represented in yaml)

The rules should look like following lines:

```bash
rule-files:
  - app-layer-events.rules
  - decoder-events.rules
  - dhcp-events.rules
  - dnp3-events.rules
  - dns-events.rules
  - feodo.rules
  - files.rules
  - http-events.rules
  - http2-events.rules
  - ipsec-events.rules
  - kerberos-events.rules
  - modbus-events.rules
  - mqtt-events.rules
  - nfs-events.rules
  - ntp-events.rules
  - smb-events.rules
  - smtp-events.rules
  - stream-events.rules
  - tls-events.rules
```


- Save and exit
- Run `service suricata restart`

## Log Rotation

To configure log rotation as per your requirement, you need to edit the following file:

vi `/etc/logrotate.d/suricata`

```
/var/log/suricata/*.log
/var/log/suricata/*.json
{
        rotate 14
        size 10M
        missingok
        compress
        copytruncate
        sharedscripts
        postrotate
                /bin/kill -HUP $(cat /var/run/suricata.pid)
        endscript
}
```
Restart `logrotate` service

```
service logrotate restart
```
