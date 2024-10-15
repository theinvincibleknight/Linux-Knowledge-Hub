# SFTP Account Creation Script

## Prerequisites
- Ubuntu server with SFTP support.
- `sudo` privileges to create users and modify directories.
- OpenSSL installed for password hashing.

## Script Details

### Script Name
`sftp_account_creation.sh`

### Script

```bash
#! /bin/bash
read -p "Enter the sftp account name: "  account
echo $account
read -p "Enter the sftp account password: "  account_pwd
echo $account_pwd
read -p "Create new SFTP account for user {$account} with password {$account_pwd}? y/n: "  prompt_confirm
if [ "$prompt_confirm" = "y" ]
then
sudo useradd -p $(openssl passwd -1 $account_pwd) -g sftp -s /sbin/nologin $account
sudo mkdir -p /sftpusers/chroot/$account
sudo chown $account:sftp /sftpusers/chroot/$account/
sudo chmod 700 /sftpusers/chroot/$account/
echo "$account created."
elif [ "$prompt_confirm" = "n" ]
then
echo "create sftp user account cancelled."
exit
else
echo "no choice selected. exiting"
fi
```

### Script Breakdown
- **Prompt for Account Name**: 
  ```bash
  read -p "Enter the sftp account name: "  account
  ```

- **Prompt for Account Password**: 
  ```bash
  read -p "Enter the sftp account password: "  account_pwd
  ```

- **Confirmation Prompt**: 
  ```bash
  read -p "Create new SFTP account for user {$account} with password {$account_pwd}? y/n: "  prompt_confirm
  ```

- **Account Creation**:
  If the user confirms with 'y':
  ```bash
  sudo useradd -p $(openssl passwd -1 $account_pwd) -g sftp -s /sbin/nologin $account
  ```

- **Directory Setup**:
  Creates a chroot directory for the SFTP user:
  ```bash
  sudo mkdir -p /sftpusers/chroot/$account
  sudo chown $account:sftp /sftpusers/chroot/$account/
  sudo chmod 700 /sftpusers/chroot/$account/
  ```

- **Feedback**:
  The script provides feedback on the account creation status:
  ```bash
  echo "$account created."
  ```

- **Cancellation and Exit**:
  If the user chooses 'n' or makes an invalid choice, the script will exit gracefully:
  ```bash
  echo "create sftp user account cancelled."
  exit
  ```

## Important Notes
- Ensure that the `sftp` group exists on your server. You can create it using:
  ```bash
  sudo groupadd sftp
  ```
- The script does not handle existing users with the same name. Ensure the account name is unique.
- The password is hashed using OpenSSL for security.

### Usage
1. Make the script executable:
   ```bash
   chmod +x sftp_account_creation.sh
   ```

2. Run the script:
   ```bash
   ./sftp_account_creation.sh
   ```

## Conclusion
This script simplifies the process of creating SFTP accounts on an Ubuntu server, ensuring that the necessary configurations and permissions are set correctly.
