# Ubuntu Server Hardening

## Securing BIOS:
- Preventing Changes to BIOS Settings
- Preventing System Booting

Securing the BIOS (Basic Input/Output System) settings is crucial for protecting the system's firmware from unauthorized access and tampering. However, it's important to note that the BIOS setup process can vary significantly depending on the hardware manufacturer and model. BIOS settings are not controlled by the operating system (like Ubuntu) but are specific to the hardware.

Here are some general steps you can take to enhance the security of the BIOS:

1. **Set a BIOS Password:**
Most BIOS setups allow you to set a password. This password will be required to access the BIOS settings during the boot process. The exact steps to set a BIOS password vary depending on the manufacturer and model of your motherboard. Consult your motherboard's manual or the manufacturer's website for instructions.

2. **Disable Booting from External Devices:**
In the BIOS settings, you can often disable booting from external devices like USB drives or optical drives. This prevents someone from booting your system with a different operating system or a malicious tool. Look for options related to "Boot Order" or "Boot Priority" in your BIOS settings.

3. **Set a Boot Password:**
Some BIOS setups allow you to set a password that is required before the system can boot. This adds an additional layer of security, even before the operating system starts. Similar to setting a BIOS password, the exact steps vary based on the hardware.

4. **Lock BIOS Settings:**
Many BIOS setups allow you to lock the BIOS settings with a password. This prevents anyone from making changes to BIOS configurations without entering the password first. This is often called "Write Protect" or "Settings Lock."

5. **Update BIOS Firmware:**
Keeping your BIOS firmware up to date is important for security. Manufacturers release updates to fix security vulnerabilities and improve system stability. Check the manufacturer's website for BIOS updates and follow their instructions for updating.

6. **Physical Security:**
Ensure that physical access to the computer is restricted. Unauthorized physical access can allow someone to reset BIOS settings or perform other malicious actions.

7. **Encryption and Secure Boot:**
Some modern systems support features like Secure Boot and TPM (Trusted Platform Module), which can enhance security by verifying the integrity of the boot process and system components.

Remember, the exact steps to perform these actions can vary depending on your hardware. Always refer to your motherboard's manual or the manufacturer's documentation for accurate instructions.

Keep in mind that BIOS security is a fundamental aspect of overall system security. While these steps help secure the BIOS, they should be part of a broader security strategy that includes maintaining a secure operating system, keeping software updated, and implementing security best practices.

## Securing Non-BIOS-based Systems:
- Unified Extensible Firmware Interface

Securing systems that use the Unified Extensible Firmware Interface (UEFI) involves protecting the firmware and ensuring the system's boot process is secure. UEFI has replaced traditional BIOS on modern systems, providing more advanced features and security mechanisms. Here's how you can enhance security for UEFI-based systems in Ubuntu:

1. **Secure Boot:**
Secure Boot is a UEFI feature that ensures only trusted software is allowed to run during the boot process. It helps prevent unauthorized or malicious code from running early in the startup sequence. To enable Secure Boot:

- Ensure that your hardware supports Secure Boot.
- Go to your system's UEFI/BIOS settings during boot (often by pressing a key like F2, F10, or Delete).
- Find the Secure Boot option and enable it.
- Import the manufacturer's Secure Boot keys or generate your own keys for signing trusted bootloaders and kernels.

2. **Update Firmware (UEFI/BIOS):**
Regularly check for and apply firmware updates from your hardware manufacturer. These updates may include security patches that address vulnerabilities.

3. **Firmware Password:**
Some UEFI systems allow you to set a password for accessing the UEFI settings. This adds an extra layer of security, preventing unauthorized changes to firmware settings.

4. **Disable Unneeded UEFI Features:**
UEFI provides various features, and some may be unnecessary for your use case. Disabling unused features reduces the attack surface. For example, you might disable network boot if not required.

5. **Check Boot Order:**
Make sure that the boot order is set to boot from the appropriate device (usually the system's primary hard drive or SSD) and that unnecessary boot options are disabled.

6. **TPM (Trusted Platform Module):**
If your system has a TPM, you can use it to enhance security by storing encryption keys and verifying system integrity during boot.

7. **Kernel and Bootloader Security:**
Ensure that the Linux kernel and bootloader are up to date. Enable features like Secure Boot and cryptographic verification of kernel modules (if available).

8. **Physical Security:**
Protect physical access to the computer. Unauthorized physical access can allow an attacker to reset UEFI settings or perform other malicious actions.

9. **Vendor-Specific Tools:**
Some hardware manufacturers provide additional tools for securing UEFI settings. Check the documentation for your system to see if any specific tools or features are available.

10. **Follow Best Practices:**
Follow general security best practices, such as using strong passwords, keeping software updated, and using firewall settings to restrict network access.

Remember that UEFI settings and options can vary depending on the manufacturer and model of your hardware. Always consult your system's documentation for accurate instructions on securing UEFI settings.

## Partitioning The Disk
- `/boot`, `/tmp`

Partitioning your disk in Ubuntu involves dividing your storage device into separate sections or partitions, each serving a specific purpose. The `/boot` and `/tmp` partitions are two such partitions that you might consider setting up for specific purposes.

 If you have live Ubuntu server deployed on AWS and you want to add separate partitions for `/boot` and `/tmp`, the process will involve resizing existing partitions and creating new ones. Here's how you can do it:

Please be aware that partitioning operations can carry risks, and you should always backup your data and be cautious during the process.

1. **Backup Important Data:**
Before you proceed with partitioning, ensure that you have backups of your important data. Partitioning operations can potentially result in data loss.

2. **Create `/tmp` Partition:**
You can create a new partition for `/tmp` by utilizing the available space in your root partition.
Use a partitioning tool like `gparted` to resize your root partition to free up space. This tool provides a graphical interface for managing disk partitions.
Once you have free space, create a new partition and set its mount point to `/tmp`.

3. **Create `/boot` Partition:**
- Since you're using an AWS instance, it's important to consider the limitations of your storage device. If `/dev/xvda` is your root device, you'll need to check whether it can be resized without data loss. Amazon EBS volumes, for example, can usually be resized without much trouble.
- If your root volume allows resizing, follow the same process as with the `/tmp` partition. Resize your root partition to create unallocated space, then create a new partition and set its mount point to `/boot`.

4. **Update /etc/fstab:**
After creating the new partitions, you'll need to update your `/etc/fstab` file to ensure that the new partitions are mounted at boot. You'll likely need to update the UUIDs of the partitions.

5. **Reboot:**
Reboot your server to apply the changes you've made. Ensure that everything is working as expected, and that your new `/tmp` and `/boot` partitions are properly mounted.

6. **Testing:**
After the reboot, make sure to test the functionality of the system, including booting, accessing `/boot`, and using `/tmp`. Make sure there are no unexpected issues.

Keep in mind that these steps can vary based on your specific AWS instance type, disk layout, and file system structure. Always take precautions, and if you're not entirely confident in performing these tasks, consider seeking assistance from someone experienced with Linux system administration and partitioning.

## Configure Security Logon Banner

> **Logon Banner:**
A C C E S S   W A R N I N G!!!!! This is a private computer system. Unauthorized access or use is prohibited and only authorized users are permitted. Use of this system constitutes consent to monitoring at all times and user should have no expectation of privacy. Unauthorized access or violations of security regulations is unlawful and hence if monitoring reveals either of it, appropriate disciplinary action will be taken against the users violating security regulations or making unauthorized use of this system.

1.	Create a Script in `/etc/update-motd.d/`:
Open a terminal or connect to your Ubuntu server via SSH and create a new script in the `/etc/update-motd.d/` directory. The script name should start with a numeric prefix to control its order of execution. For example, create a script named `99-my-banner`:

```bash
sudo nano /etc/update-motd.d/99-my-banner
```

2.	Add the Banner Message to the Script:
Inside the script, you can use standard shell commands to generate your custom banner message. For example, to display a simple welcome message, you can use `echo` commands:

```bash
#!/bin/sh
echo "********************************************************************************"
echo "A C C E S S   W A R N I N G!!!!!"
echo "This is a private computer system. Unauthorized access or use is prohibited and only authorized users are permitted. Use of this system constitutes consent to monitoring at all times and user should have no expectation of privacy. Unauthorized access or violations of security regulations is unlawful and hence if monitoring reveals either of it, appropriate disciplinary action will be taken against the users violating security regulations or making unauthorized use of this system."
echo "********************************************************************************"
```

Save the file and exit the text editor.

3.	Set Appropriate Permissions:

Ensure the script is executable:

```bash
sudo chmod +x /etc/update-motd.d/99-my-banner
```

4.	Test the Banner:
To test the banner, you can either log out and log back in, or you can force the update by running the following command:

```bash
sudo run-parts /etc/update-motd.d/
```

The custom banner message will be displayed after a successful login.

5.	(Optional) Disable Other MOTD Scripts:
By default, Ubuntu includes several scripts in the `/etc/update-motd.d/` directory that generate system information. If you prefer to show only your custom banner, you can remove or disable the other scripts. For example, you can move the existing scripts to a different directory:

```bash
sudo mkdir /etc/update-motd.d-backup
sudo mv /etc/update-motd.d/* /etc/update-motd.d-backup/
```

> `Note`: Disabling or removing existing MOTD scripts may hide useful system information for your users. Be cautious and consider the impact on your server administration.

## Post-Installation Procedures

In Ubuntu, the package manager is `apt` (Advanced Package Tool) and the default firewall management tool is `ufw` (Uncomplicated Firewall). Here are the equivalent post-installation procedures for Ubuntu:

1. **Update Packages:**
To update your system packages, use the `apt` package manager:

```bash
sudo apt update
sudo apt upgrade
```

To check the current and installed kernel versions on an Ubuntu system, you can use the `uname` and `dpkg` commands respectively. Here's how:

- To check the current kernel version (the kernel currently running on your system), use the `uname` command with the `-r` option:

```bash
uname -r
```

This will display the current kernel version, such as "5.4.0-81-generic".

- To list all installed kernel packages on your Ubuntu system, use the `dpkg` command with `grep` to filter for packages whose name starts with "linux-image":

```bash
dpkg -l | grep linux-image
```

This will show a list of installed kernel packages along with their version numbers. The current kernel (from the previous step) will be marked with "ii" in the first column.

Example output:
```bash
ii  linux-image-5.4.0-81-generic    5.4.0-81.91
ii  linux-image-5.4.0-84-generic    5.4.0-84.94
```

The version number is listed in the second column.

Please note that the actual output might vary based on your Ubuntu version and any kernel updates you have installed.

2. **Enable and Start Firewall (ufw):**
`ufw` is a front-end for managing iptables firewall rules in Ubuntu. To enable and start `ufw`, follow these steps:

```bash
sudo ufw enable
sudo ufw status
```

The `enable` command turns on the firewall, and `status` will show the current firewall rules and status.

3. **Disable Unnecessary Services:**
Ubuntu, like CentOS, allows you to disable services you do not need to improve security and reduce potential attack surfaces. To disable a service, use the `systemctl` command.

For example, to disable the CUPS (Common UNIX Printing System) service, you can run:

```bash
sudo systemctl disable cups
```

Replace `cups` with the service name you want to disable.

Remember that before disabling any service, you should carefully consider whether you truly do not need it, as some services might be required for specific functionalities of your system or applications.

## Securing the Boot Loader

Securing the GRUB 2 bootloader in Ubuntu by setting a password to prevent unauthorized access to its console involves a few steps. Here's how you can do it:

1. **Generate an Encrypted Password:**

First, you need to generate an encrypted password that GRUB will use for authentication. You can do this using the `grub-mkpasswd-pbkdf2` command. Open a terminal and enter the following command:

```bash
grub-mkpasswd-pbkdf2
```

You'll be prompted to enter a password, and the command will generate an encrypted string. Keep this encrypted string handy, as you'll need it later.

2. **Edit the GRUB Configuration:**

Open the GRUB configuration file using a text editor. You'll need administrative privileges to do this, so use the `sudo` command:

```bash
sudo nano /etc/default/grub
```

3. **Configure the GRUB Password:**

In the configuration file, look for the line that starts with `GRUB_CMDLINE_LINUX`. Add the following options to the line:

```bash
GRUB_CMDLINE_LINUX="password_pbkdf2 root (hd0,1) your_encrypted_password"
```

Replace `(hd0,1)` with the appropriate disk and partition for your system (use GRUB-style notation, such as `(hd0,1)` for the first hard drive and second partition).

Replace `your_encrypted_password` with the encrypted password generated in step 1.

4. **Update GRUB Configuration:**

After making the changes, save the file and exit the text editor.

Update the GRUB configuration by running the following command:

```bash
sudo update-grub
```

5. **Reboot:**

Reboot your system to apply the changes:

```bash
sudo reboot
```

6. **Test the Password Protection:**

After rebooting, you should see a GRUB menu with a locked command line. To access the command line, press `c` and then enter the `ls` command to list the partitions. You'll be prompted to enter the password. Once you enter the correct password, you'll gain access to the GRUB command line.

This process will help you secure the GRUB 2 bootloader in Ubuntu by password-protecting the access to its console. Keep in mind that physical access to the system can potentially bypass this protection, so it's just one layer of security.

## Password Security

In Linux systems, including Ubuntu, the `/etc/passwd` and `/etc/shadow` files are critical for user account management and password security. However, there are certain measures you can take to enhance password security and protect these files:

1. **Limit Access to /etc/shadow:**

The `/etc/shadow` file stores the hashed passwords and other security-related information for user accounts. This file should only be accessible by the root user and the `shadow` group. You can use the following command to set the appropriate permissions:

```bash
sudo chown root:shadow /etc/shadow
sudo chmod 640 /etc/shadow
```

This ensures that only the root user and members of the `shadow` group have read access to the file.

2. **Limit Access to /etc/passwd:**

The `/etc/passwd` file contains basic user account information. While it doesn't store passwords directly, it's still important to limit access to this file. You can set the following permissions:

```bash
sudo chown root:root /etc/passwd
sudo chmod 644 /etc/passwd
```
This allows read access to all users but only root can modify the file.

## Forcing Strong Passwords:

To enforce a strong password policy in Ubuntu, you can use the libpwquality library and the pam_pwquality PAM module (Pluggable Authentication Modules). This will allow you to set requirements such as minimum password length, minimum character classes, and check for character sequences and consecutive characters.

To enforce strong password policies through the PAM configuration, follow these steps:

1.	**Install `libpwquality`:**

```bash
sudo apt update
sudo apt install libpwquality-tools
```

2.	**Backup PAM Configuration Files:**

Before making any changes, create backups of the PAM configuration files you intend to modify. This will allow you to revert to the original configuration if needed.

```bash
sudo cp /etc/pam.d/common-password /etc/pam.d/common-password.bak
sudo cp /etc/pam.d/common-auth /etc/pam.d/common-auth.bak
```

3.	**Edit the PAM Configuration File:**

Open the `/etc/pam.d/common-password` file in a text editor:

```bash
sudo nano /etc/pam.d/common-password
```
Locate the line that contains `password requisite pam_pwquality.so` and comment it out or remove it:

```
# password requisite pam_pwquality.so retry=3
```

Then, add the following lines at the end of the file to enforce the desired password complexity requirements:

```
password requisite pam_pwquality.so retry=3 minlen=8 minclass=4 maxsequence=3 maxrepeat=3
```

Save and close the file.

4.	**Edit common-auth File:**

If you want to enforce password complexity requirements during the user's initial login or password change, you can also modify the `common-auth` file:

```bash
sudo nano /etc/pam.d/common-auth
```

Add the following line at the top of the file:

```bash
auth requisite pam_pwquality.so retry=3
```

This will ensure that users are prompted to set a strong password when they create a new account or change their password.

Save and close the file.

5.	**Configure `pwquality.conf`:**

Open the `pwquality.conf` file in a text editor:

```bash
sudo nano /etc/security/pwquality.conf
```

Add the following lines to set the desired password policy options:

```
minlen = 8
minclass = 4
maxsequence = 3
maxrepeat = 3
```

Save the file and exit the text editor.

6.	**Restart the Authentication Service:**

After making the above changes, restart the authentication service to apply the modifications:

```bash
sudo systemctl restart systemd-logind
```

7.	**Test the Password Policy:**

After making the changes, test the new password policy by creating a new user or changing the password for an existing user. The system should enforce the specified complexity requirements during the process.

> **Note**: While switching user `su – <username>` if you see any error such as `su: Module is unknown`, undo the changes done to `common-auth` file. Restart the `systemd-logind` service and try again to switch.

Keep in mind that manually editing PAM configuration files can be powerful, but it requires careful attention to avoid misconfigurations that could lead to unintended consequences. Always make backups and test your changes to ensure that the desired behavior is achieved.

## Configuring Password Aging

In Ubuntu, you can use the `chage` command to configure password aging for a user account. The `chage` command allows you to set various password-related attributes, including the password expiration date, minimum and maximum password age, and warning period before the password expires.

To configure the password aging for a user account to match your Active Directory policy or set it to at least 30 days, follow these steps:

1.	**Check Current Password Aging Settings:**

Before making any changes, you can check the current password aging settings for a specific user account using the `chage -l` command:

```bash
sudo chage -l username
```

Replace `username` with the actual username of the account you want to check.

2.	**Set Password Expiration Date:**

To configure the password expiration date for a user, use the `chage -E` option followed by the desired expiration date in **YYYY-MM-DD** format:

```bash
sudo chage -E YYYY-MM-DD username
```

Replace `YYYY-MM-DD` with the desired date for the password to expire and `username` with the actual username of the account.

3.	**Set Minimum and Maximum Password Age:**

You can set the minimum and maximum password age using the `chage -m` and `chage -M` options, respectively:

```bash
sudo chage -m MIN_DAYS username
sudo chage -M MAX_DAYS username
```

Replace `MIN_DAYS` and `MAX_DAYS` with the desired values for the minimum and maximum password age, and `username` with the actual username of the account.

For example, to set the minimum password age to 1 day and the maximum password age to 30 days:

```bash
sudo chage -m 1 username
sudo chage -M 30 username
```

4.	Set Password Expiry Warning Period:
You can set the number of days before the password expires that the user will receive a warning using the `chage -W` option:

```bash
sudo chage -W DAYS username
```

Replace `DAYS` with the desired number of days for the warning period and `username` with the actual username of the account.

For example, to set a warning period of 7 days:

```bash
sudo chage -W 7 username
```

After configuring the password aging settings using the `chage` command, the user's password will expire based on the specified configurations, and they will be prompted to change their password accordingly.

## Account Locking

To implement a lockout policy for non-root users after three unsuccessful login attempts and an automatic unlock after 10 minutes on an Ubuntu server, you can follow these steps:

Here are the steps to set up user lockout using pam_faillock:

1.	**PAM Modules Installation:**

Check if the `pam_faillock` module is installed on your system. If not, you might need to install it. On Ubuntu, you can install it with:

```bash
sudo apt-get install libpam-modules
```

2.	**Edit PAM Configuration:**

Open the PAM configuration file for SSH:

```bash
sudo nano /etc/pam.d/sshd
```

3.	**Add the Lockout Rules:**

Add the following lines at the top of the file:

```bash
auth required pam_faillock.so preauth audit silent deny=3 unlock_time=600
auth [default=die] pam_faillock.so authfail audit deny=3 unlock_time=600
```

4.	**Restart Service:** 

After making changes to PAM configuration files, you need to restart the corresponding service to apply the changes. For SSH, you can restart it with:

```bash
sudo service ssh restart
```

5.	**Testing the Lockout:**

Make sure you're using a non-root user (like "webadmin") to test this. If you're logged in as root, the rules won't apply to the root account.
Attempt incorrect logins to trigger the lockout. Enter the wrong password three times.
Wait for the specified lockout time (600 seconds = 10 minutes).

After the lockout time has passed, attempt to log in again with the correct password.

6.	**Check User's Lockout Status:**

Use the faillock command to check the lockout status of a specific user. Replace username with the actual username you want to check:

```bash
sudo faillock --user username
```

7.	To check if an account is locked on a Linux system, you can use the passwd command with the -S option followed by the username. This will display information about the account's password status, including whether it's locked. Here's how you can do it:

```bash
passwd -S <username>
```

- Example:
```
# passwd -S webadmin
webadmin P 08/03/2023 1 30 7 -1
```

1.	`P`: This indicates that the password is in a usable state.

2.	`08/03/2023`: The date when the password was last changed.

3.	`1`: The minimum number of days before the password can be changed.

4.	`30`: The maximum number of days after which the password must be changed.

5.	`7`: The number of days before the password expires during which the user will be warned about the expiration.

6.	`-1`: The number of days after the password expires during which the user can still change the password (negative value indicates that there's no enforcement).

If an account is locked, the output would typically show "L" or "LK" in place of "P" to indicate that the password is locked.

8.	**Check Logs:** Monitor the system logs for any relevant error messages. The logs can provide insights into what might be causing the issue. Check `/var/log/auth.log` or similar files for authentication-related information.

9. **Reboot:** In some cases, a reboot might help apply PAM changes and clear any issues that might be causing unexpected behavior.

