# User Creation Script

The script creates a new user with the specified username, disables the password, and sets up SSH access for the user. It is designed to be run in a terminal on a Linux system with `sudo` privileges.

## Prerequisites

- A Linux system with `sudo` privileges.
- Bash shell.

## Usage

To use the script, follow these steps:

1. **Open a terminal.**
2. **Copy the script** into a file, for example, `create_user.sh`.

   ```bash
   #!/bin/bash

   # Check if a username was provided as an argument
   if [ -z "$1" ]; then
     echo "Usage: $0 <username>"
     exit 1
   fi

   # Assign the first argument to the USERNAME variable
   USERNAME=$1

   # Create the new user
   sudo adduser --disabled-password --gecos "" "$USERNAME"

   # Remove the password
   sudo passwd -d "$USERNAME"

   # Set password expiry and other properties
   sudo chage -I -1 -m 0 -M 99999 -E -1 "$USERNAME"

   # Switch to the new user to set up SSH
   sudo su - "$USERNAME" -c "
   mkdir -p ~/.ssh
   chmod 700 ~/.ssh
   touch ~/.ssh/authorized_keys
   chmod 600 ~/.ssh/authorized_keys
   "

   echo "User '$USERNAME' has been created and configured."
   ```

3. **Make the script executable**:

   ```bash
   chmod +x create_user.sh
   ```

4. **Run the script with the desired username**:

   ```bash
   ./create_user.sh <username>
   ```

   Replace `<username>` with the desired username for the new user.

## Script Functionality

- **Checks for Username**: The script checks if a username is provided as an argument. If not, it displays usage instructions and exits.
- **Creates User**: It creates a new user with the specified username, disabling the password and setting the user information to empty.
- **Removes Password**: The password for the new user is removed, allowing for passwordless login.
- **Sets Password Expiry**: Configures the password expiry settings for the user.
- **Sets Up SSH Access**: Creates the `.ssh` directory and `authorized_keys` file for the new user, setting appropriate permissions.

## Important Notes

- Ensure that you have the necessary permissions to run the script with `sudo`.
- The script does not set a password for the new user, which may not be suitable for all environments. Consider security implications before using passwordless accounts.
- You may need to manually add SSH keys to the `~/.ssh/authorized_keys` file for the new user to enable SSH access.
