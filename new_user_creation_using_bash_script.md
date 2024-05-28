## Bash script to automate the steps to create a non-root user in Ubuntu, given a username as an argument.

Save the following script as create_user.sh:

```bash
#!/bin/bash

# Check if a username was provided as an argument
if [ -z "$1" ]; then
  echo "Usage: $0 <username>"
  exit 1
fi

# Assign the first argument to the USERNAME variable
USERNAME=$1

# Check if the user already exists
#if id "$USERNAME" &>/dev/null; then
#  echo "User '$USERNAME' already exists."
#  exit 1
#fi

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

### Usage

To create a new user using this script, run it from the terminal and provide the username as an argument. For example:

```bash
sudo bash create_user.sh <username>
```
