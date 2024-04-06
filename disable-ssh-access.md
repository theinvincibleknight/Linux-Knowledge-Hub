# Disable SSH access for a non-root user in Ubuntu

## Method 1: Modify User's Shell
1. Change User's Shell:

You can change the user's shell to something like `/sbin/nologin` or `/bin/false`. This prevents the user from logging in via SSH.

```bash
sudo usermod -s /sbin/nologin username
```
Replace `username` with the non-root user's username.

2. To Enable SSH access back

To enable SSH access for a user after disabling it using `/sbin/nologin`, you'll need to change their shell back to a valid login shell. Here's how you can do it:

```bash
sudo usermod -s /bin/bash username
```
Replace `username` with the username of the user for whom you want to enable SSH access.

This command changes the user's shell back to `/bin/bash`, allowing them to log in via SSH again.

## Method 2: Update SSH Configuration
1. Edit SSH Configuration:

Edit the SSH configuration file `/etc/ssh/sshd_config` using a text editor like `nano` or `vim`.

```bash
sudo nano /etc/ssh/sshd_config
```
2. Deny User's Access:

Add the following line at the end of the file to deny SSH access for the specific user:

```bash
DenyUsers username
```
Replace `username` with the non-root user's username.

3. Restart SSH Service:

After saving the changes, restart the SSH service for the changes to take effect.

```bash
sudo systemctl restart sshd
```

4. To Enable SSH access back

Remove this line or comment it out by adding a `#` at the beginning of the line.

```bash
# DenyUsers username
```

After making the changes, restart the SSH service for the changes to take effect.

```bash
sudo systemctl restart sshd
```

> Note
- Make sure you have administrative privileges (`sudo`) to perform these actions.
- Disabling SSH access using Method 1 allows the user to continue using other services on the system, while Method 2 denies access only to SSH. Choose the method based on your requirements.
