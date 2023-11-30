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

## feodo.rules file

You can create this `feodo.rules` file once, and copy the same to the required servers after installing the Suricata. And configure the rule in `/etc/suricata/suricata.yaml` file.

```bash
$ cat /etc/suricata/rules/feodo.rules
################################################################
# abuse.ch Feodo Tracker Suricata / Snort Ruleset              #
# Last updated: 2023-10-30 07:40:30 UTC                        #
#                                                              #
# Terms Of Use: https://feodotracker.abuse.ch/blocklist/       #
# For questions please contact feodotracker [at] abuse.ch      #
################################################################
#
reject tcp $HOME_NET any -> [178.128.23.9] 4125 (msg:"Feodo Tracker: potential Dridex CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/178.128.23.9/; sid:900505872; rev:1;)
reject tcp $HOME_NET any -> [192.99.150.39] 7443 (msg:"Feodo Tracker: potential Dridex CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/192.99.150.39/; sid:900506412; rev:1;)
reject tcp $HOME_NET any -> [212.112.86.37] 9676 (msg:"Feodo Tracker: potential Dridex CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/212.112.86.37/; sid:900506750; rev:1;)
reject tcp $HOME_NET any -> [198.199.70.22] 6602 (msg:"Feodo Tracker: potential Dridex CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/198.199.70.22/; sid:900507094; rev:1;)
reject tcp $HOME_NET any -> [103.109.247.10] 10443 (msg:"Feodo Tracker: potential Dridex CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/103.109.247.10/; sid:900507312; rev:1;)
reject tcp $HOME_NET any -> [129.232.146.250] 443 (msg:"Feodo Tracker: potential Dridex CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/129.232.146.250/; sid:900507375; rev:1;)
reject tcp $HOME_NET any -> [144.91.122.94] 443 (msg:"Feodo Tracker: potential Dridex CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/144.91.122.94/; sid:900507519; rev:1;)
reject tcp $HOME_NET any -> [103.233.103.85] 443 (msg:"Feodo Tracker: potential Dridex CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/103.233.103.85/; sid:900510081; rev:1;)
reject tcp $HOME_NET any -> [132.148.79.222] 2222 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/132.148.79.222/; sid:900512335; rev:1;)
reject tcp $HOME_NET any -> [192.9.135.73] 1194 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/192.9.135.73/; sid:900512344; rev:1;)
reject tcp $HOME_NET any -> [132.148.73.117] 2222 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/132.148.73.117/; sid:900512345; rev:1;)
reject tcp $HOME_NET any -> [148.153.34.82] 2078 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/148.153.34.82/; sid:900513350; rev:1;)
reject tcp $HOME_NET any -> [104.243.45.170] 2222 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/104.243.45.170/; sid:900513353; rev:1;)
reject tcp $HOME_NET any -> [38.242.240.28] 1194 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/38.242.240.28/; sid:900513355; rev:1;)
reject tcp $HOME_NET any -> [102.129.139.65] 32999 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/102.129.139.65/; sid:900513358; rev:1;)
reject tcp $HOME_NET any -> [45.182.189.105] 443 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/45.182.189.105/; sid:900513360; rev:1;)
reject tcp $HOME_NET any -> [185.106.94.174] 5000 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/185.106.94.174/; sid:900513361; rev:1;)
reject tcp $HOME_NET any -> [88.214.27.74] 443 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/88.214.27.74/; sid:900513362; rev:1;)
reject tcp $HOME_NET any -> [78.128.112.208] 443 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/78.128.112.208/; sid:900513363; rev:1;)
reject tcp $HOME_NET any -> [80.85.140.152] 5938 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/80.85.140.152/; sid:900513364; rev:1;)
reject tcp $HOME_NET any -> [185.106.94.177] 13721 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/185.106.94.177/; sid:900513365; rev:1;)
reject tcp $HOME_NET any -> [80.85.140.43] 9785 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/80.85.140.43/; sid:900513366; rev:1;)
reject tcp $HOME_NET any -> [185.106.94.152] 13720 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/185.106.94.152/; sid:900513367; rev:1;)
reject tcp $HOME_NET any -> [91.215.85.216] 443 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/91.215.85.216/; sid:900513368; rev:1;)
reject tcp $HOME_NET any -> [91.215.85.154] 443 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/91.215.85.154/; sid:900513369; rev:1;)
reject tcp $HOME_NET any -> [91.215.85.197] 443 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/91.215.85.197/; sid:900513370; rev:1;)
reject tcp $HOME_NET any -> [109.107.182.10] 443 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/109.107.182.10/; sid:900513371; rev:1;)
reject tcp $HOME_NET any -> [109.107.182.11] 443 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/109.107.182.11/; sid:900513372; rev:1;)
reject tcp $HOME_NET any -> [15.235.143.190] 2224 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/15.235.143.190/; sid:900513374; rev:1;)
reject tcp $HOME_NET any -> [155.138.156.94] 5243 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/155.138.156.94/; sid:900513375; rev:1;)
reject tcp $HOME_NET any -> [51.68.146.19] 5242 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/51.68.146.19/; sid:900513376; rev:1;)
reject tcp $HOME_NET any -> [154.221.30.136] 13724 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/154.221.30.136/; sid:900513377; rev:1;)
reject tcp $HOME_NET any -> [154.92.19.139] 2222 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/154.92.19.139/; sid:900513378; rev:1;)
reject tcp $HOME_NET any -> [139.99.216.90] 13720 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/139.99.216.90/; sid:900513379; rev:1;)
reject tcp $HOME_NET any -> [156.251.137.134] 5000 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/156.251.137.134/; sid:900513380; rev:1;)
reject tcp $HOME_NET any -> [154.12.252.84] 23399 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/154.12.252.84/; sid:900513381; rev:1;)
reject tcp $HOME_NET any -> [85.215.218.128] 5243 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/85.215.218.128/; sid:900513382; rev:1;)
reject tcp $HOME_NET any -> [103.231.93.15] 5631 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/103.231.93.15/; sid:900513383; rev:1;)
reject tcp $HOME_NET any -> [196.218.123.202] 13783 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/196.218.123.202/; sid:900513384; rev:1;)
reject tcp $HOME_NET any -> [45.33.76.163] 2223 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/45.33.76.163/; sid:900513386; rev:1;)
reject tcp $HOME_NET any -> [139.177.198.199] 2226 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/139.177.198.199/; sid:900513387; rev:1;)
reject tcp $HOME_NET any -> [172.234.29.13] 2224 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/172.234.29.13/; sid:900513388; rev:1;)
reject tcp $HOME_NET any -> [172.233.187.145] 2226 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/172.233.187.145/; sid:900513389; rev:1;)
reject tcp $HOME_NET any -> [172.233.186.50] 5632 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/172.233.186.50/; sid:900513390; rev:1;)
reject tcp $HOME_NET any -> [198.244.141.4] 9785 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/198.244.141.4/; sid:900513391; rev:1;)
reject tcp $HOME_NET any -> [176.58.102.36] 2225 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/176.58.102.36/; sid:900513392; rev:1;)
reject tcp $HOME_NET any -> [217.69.8.229] 13782 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/217.69.8.229/; sid:900513393; rev:1;)
reject tcp $HOME_NET any -> [45.79.147.119] 9785 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/45.79.147.119/; sid:900513394; rev:1;)
reject tcp $HOME_NET any -> [139.144.215.192] 13785 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/139.144.215.192/; sid:900513395; rev:1;)
reject tcp $HOME_NET any -> [172.232.24.58] 2226 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/172.232.24.58/; sid:900513396; rev:1;)
reject tcp $HOME_NET any -> [154.61.75.156] 2078 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/154.61.75.156/; sid:900513397; rev:1;)
reject tcp $HOME_NET any -> [216.128.176.211] 2222 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/216.128.176.211/; sid:900513398; rev:1;)
reject tcp $HOME_NET any -> [139.144.31.103] 1194 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/139.144.31.103/; sid:900513399; rev:1;)
reject tcp $HOME_NET any -> [45.79.174.92] 1194 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/45.79.174.92/; sid:900513400; rev:1;)
reject tcp $HOME_NET any -> [185.106.94.167] 5631 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/185.106.94.167/; sid:900513401; rev:1;)
reject tcp $HOME_NET any -> [85.209.11.185] 2222 (msg:"Feodo Tracker: potential QakBot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/85.209.11.185/; sid:900513402; rev:1;)
reject tcp $HOME_NET any -> [65.20.82.17] 5938 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/65.20.82.17/; sid:900513403; rev:1;)
reject tcp $HOME_NET any -> [172.234.16.175] 2083 (msg:"Feodo Tracker: potential Pikabot CnC Traffic detected"; threshold: type limit, track by_src, seconds 60, count 1; classtype:trojan-activity; reference:url, feodotracker.abuse.ch/browse/host/172.234.16.175/; sid:900513404; rev:1;)

```
