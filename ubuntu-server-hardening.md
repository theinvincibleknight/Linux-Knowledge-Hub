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
