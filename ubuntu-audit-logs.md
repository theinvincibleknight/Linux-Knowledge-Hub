# Audit Logs

## 1. Install Auditd:

Auditd is the user space component to the Linux Auditing System. It allows you to track system calls and create detailed logs about system activities.

```bash
sudo apt-get update
sudo apt-get install auditd
```

## 2. Enable and Start Auditd:

After installation, start and enable the auditd service.

```bash
sudo systemctl start auditd
sudo systemctl enable auditd
```
## 3. Auditd Persistence:

Some Linux distributions have a mechanism to manage audit rules that persist across reboots. Instead of directly editing the `audit.rules` file, use the `auditctl` command to add and manage rules.

To add a rule, use the `auditctl` command. For example:

```bash
sudo auditctl -w /var/run/utmp -p wa -k logins
```

This will add the rule to the active rule set.

## 4. Auditd Service Restart:

If you've been manually editing the `audit.rules` file, remember to restart the auditd service after making changes to ensure the new rules take effect:

```bash
sudo systemctl restart auditd
```

## 5. View Audit Logs:
Audit logs are stored in `/var/log/audit/audit.log`. You can use `ausearch` or other tools to query and analyze the logs.

To view all audit logs related to logins:

```bash
ausearch -i -k logins
```
The `-i` option in the command provides additional information about the events.

Shows audit events related to rule modifications made using auditctl. This output indicates that audit rules were added, changed, and removed. Each entry provides information about the type of event, the action performed, and relevant details.

Audit logs are stored in the `/var/log/audit/audit`.log file by default. You can view the audit log by directly examining this file:

```bash
sudo less /var/log/audit/audit.log
```

# Audit Rules:

To configure audit rules in Ubuntu using the `/etc/audit/audit.rules` file and the `/etc/audit/rules.d/` directory, you can follow these steps:

1.	**Understanding Rule Groups:**

The `/etc/audit/audit.rules` file and the `/etc/audit/rules.d/` directory are used to define different sets of audit rules. These rules specify what events to monitor and log. The numbers preceding the rule filenames indicate the priority and grouping of the rules.

- `10`: Kernel and auditctl configuration
- `20`: Rules that could match general rules but you want a different match
- `30`: Main rules
- `40`: Optional rules
- `50`: Server-specific rules
- `70`: System local rules
- `90`: Finalize (immutable)

2.	**Configuring Audit Rules:**

Here are some example rules for each of the rule groups:

1.	**10: Kernel and auditctl configuration:**

These rules are typically used to configure global audit settings and set up initial monitoring.

•	Enable auditd startup at boot:

```bash
# /etc/audit/rules.d/10-configuration.rules
-e 2
```

•	Set auditd backlog limit:

```bash
# /etc/audit/rules.d/10-configuration.rules
-b 8192
```

2.	**20: Rules that could match general rules but you want a different match:**

These rules are used when you want specific rules to take precedence over more general rules.

•	Log failed login attempts (preferably using a system-specific rule):

```bash
# /etc/audit/rules.d/20-specific-login.rules
-a always,exit -F arch=b64 -S sethostname -F success=0 -k login-failure
```

3.	30: Main rules:

These rules capture the core events you want to monitor. Note that these examples are basic; you might want to expand them based on your needs.

•	Monitor file read and write actions:

```bash
# /etc/audit/rules.d/30-file-access.rules
-a always,exit -F arch=b64 -S open,creat,truncate,ftruncate,openat,open_by_handle_at -k file-access
```

•	Monitor changes to system administration files:

```bash
# /etc/audit/rules.d/30-admin-files.rules
-a always,exit -F arch=b64 -S chmod,chown,chgrp,fchmod,fchown,fchownat -k admin-file-change
```

4.	**40: Optional rules:**

These rules can include additional events you consider important to monitor, but not critical.

•	Monitor changes to network configuration:

```bash
# /etc/audit/rules.d/40-network.rules
-a always,exit -F arch=b64 -S sethostname,setdomainname -k network-change
```

5.	**50: Server-specific rules:**

These rules can be tailored to your server's specific requirements and services.

•	Monitor changes to web server configuration files:

```bash
# /etc/audit/rules.d/50-web-server.rules
-a always,exit -F arch=b64 -S chmod,chown,chgrp,fchmod,fchown,fchownat -F path=/etc/nginx/* -k web-config-change
```

6.	**70: System local rules:**

These rules focus on local system actions that might be relevant to your environment.

•	Monitor changes to user and group information:

```bash
# /etc/audit/rules.d/70-user-group.rules
-a always,exit -F arch=b64 -S useradd,usermod,userdel,groupadd,groupmod,groupdel -k user-group-change
```

7.	**90: Finalize (immutable):**

These rules are meant to enforce immutability on the audit configuration itself.

•	Lock the audit configuration to prevent changes:

```bash
# /etc/audit/rules.d/90-finalize.rules
-e 2
-a always,exit -F arch=b64 -S auditctl -F success=0 -k audit-config-change
```
Remember that these are just examples, and you should tailor them to your environment's specific requirements. After adding or modifying rules, always reload the audit rules using `sudo service auditd reload` for the changes to take effect. Additionally, monitor the audit logs to ensure that the configured rules are capturing the desired events.

<blockquote>

[OPTIONAL]

### Log Rotation:
Audit logs can grow large over time.  By default, most Linux distributions are configured to rotate and retain audit logs automatically. If not set, consider setting up log rotation to manage the log file size:

```bash
sudo nano /etc/audit/auditd.conf
```

Find and update the max_log_file parameter:

```bash
max_log_file = <size in MB>
```
Then restart the auditd service.

```bash
sudo systemctl restart auditd
```

1. **Log Rotation:** When the audit log file (`audit.log`) reaches a certain size, it is rotated. Log rotation involves renaming the current log file to `audit.log.1` and creating a new `audit.log` file to continue logging events.

2. **Retention Policy:** The `/etc/audit/auditd.conf` file includes settings related to log retention. Specifically, `the max_log_file` and `num_logs` options determine how many log files are retained and their maximum size. For example, if `max_log_file` is set to 50 MB and `num_logs` is set to 5, the audit system will retain up to 5 log files, each of which can be up to 50 MB in size. Older log files are deleted automatically when new ones are created.

3. **Log Compression:** Older log files that are rotated may be compressed to save disk space. Compressed log files have extensions like `.gz` (e.g., `audit.log.1.gz`). The compression helps reduce storage space usage.

If you need to change the default log retention settings, you can modify the `/etc/audit/auditd.conf` file to adjust the max_log_file and num_logs values to suit your needs. After making changes, you may need to restart the auditd service for the new settings to take effect.

</blockquote>

You can check if the audit rules are applied to the system in Ubuntu using the `auditctl` command. 

Use the `auditctl -l` command to list the currently loaded audit rules. This will show you the active rules:

```bash
sudo auditctl -l
```

If you want to check if a specific rule is loaded, you can use `grep` to search for it in the output. For example:

```bash
sudo auditctl -l | grep "10-configuration.rules"
```
This will check if the 10-configuration.rules rule is loaded.

If you have created an audit rule file, but the rules are not showing up when you run `sudo auditctl -l`, it's possible that the rules have not been applied or loaded into the audit subsystem.

To load all the audit rules from `/etc/audit/rules.d/`, you can use the `auditctl` command. Here's how you can do it:

```bash
auditctl -R /etc/audit/rules.d/
```

To see the any particular rule loaded or not, you can use the -R option with `auditctl`

```bash
auditctl -R /etc/audit/rules.d/10-configuration.rules
```

This command will load the rules from the specified file, but it won't make them active immediately. To activate the rules, you can either restart the `auditd` service or use `auditctl` with the `-e` option.

To activate the rules immediately, you can use:

```bash
auditctl -e 1
```
This sets the audit daemon into enabled mode (1), which will activate the loaded rules.

After activating the rules, you can check if they are active by running:

```bash
auditctl -l
```

# Shell History

To check the shell history of a particular user on an Ubuntu system, you can examine the user's shell history file.

## 1. Viewing the History File:

The shell history is stored in the user's home directory in a file named .bash_history for the Bash shell.

```bash
cat /home/<username>/.bash_history
```

## 2. Searching the history

You can use the grep command to search for specific commands within the user's shell history. This can be helpful when you're looking for a specific command or pattern.

```bash
grep "search_string" /home/<username>/.bash_history
```

Replace **"search_string"** with the command or keyword you're looking for.

## 3. View Command History with Timestamps:
Open a terminal and use the following commands to view the command history of the user along with date and time information:

```bash
HISTTIMEFORMAT="%Y-%m-%d %T"
history
```

This command sets the `HISTTIMEFORMAT` variable to the desired timestamp format (Year-Month-Day Hour:Minute:Second) and then displays the command history with timestamps.

[Optional] To set date and time format to command history, open your Bash configuration file in a text editor. This is typically `~/.bashrc` for individual users or `/etc/bash.bashrc` for system-wide configuration. Use the appropriate file based on your needs:

For the current user:

```bash
nano ~/.bashrc
```

For the system-wide configuration (requires root privileges):

```bash
sudo nano /etc/bash.bashrc
```
Add the following lines at the end of the file to set the `HISTTIMEFORMAT` environment variable:

```bash
# Set the timestamp format for command history
export HISTTIMEFORMAT="%F %T "
```
This configuration sets the timestamp format to **"YYYY-MM-DD HH:MM:SS"**.

Save the file and exit the text editor.

To apply the changes to your current session, either close and reopen your terminal or run:

```bash
source ~/.bashrc   # For individual user configuration
```
or

```bash
source /etc/bash.bashrc   # For system-wide configuration
```
Now, when you view your shell history using the `history` command, it will display each command with the timestamp of when it was executed.

## 4. Searching the History:
You can use the `grep` command along with the `HISTTIMEFORMAT` setting to search for specific commands or patterns within the history:

```bash
HISTTIMEFORMAT="%Y-%m-%d %T"
history | grep "search_string"
```

Replace `"search_string"` with the command or keyword you're looking for.

# Authentication Logs

These logs record login attempts and other authentication-related information, including the IP addresses or sources of the connections. On Ubuntu systems, the authentication logs are stored in the `/var/log/auth.log` file.

## 1. View the Authentication Logs:
Open the `/var/log/auth.log` file using a text editor or a command-line tool. You will need superuser privileges to view this file.

```bash
sudo less /var/log/auth.log
```
## 2. Search for the User and Command:
Within the `auth.log` file, you can search for the user's name and the command you're interested in. Use a tool like `grep` to narrow down the results. Replace `<username>` with the actual username of the non-root user and `<command>` with the command executed.

```bash
sudo grep "<username>" /var/log/auth.log | grep "<command>"
```

This command will display the authentication logs related to the specified user and command. Look for entries that indicate successful logins or executed commands.

## 3. Check IP Addresses or Sources:
In the search results, you will see entries with IP addresses or sources from which the user logged in. These entries typically include the IP address or hostname in the `from` field.

**For Example:**

```
Aug 30 10:00:00 hostname sshd[12345]: Accepted password for <username> from <source_IP> port <source_port> ssh2
```

In this example, `<source_IP>` and `<source_port>` indicate the IP address and port from which the user logged in.

Please note that the `auth.log` file contains a variety of authentication-related events, including SSH logins, sudo command executions, and more. You'll need to review the relevant entries to find the information you're looking for.
