# Bash script to copy latest files from your Ubuntu server to remote SFTP server

## Details:

**Ubuntu Server**
- **Local Directory:** /sftpusers/chroot/sftp-dev-dir/bills

**Remote SFTP Server**
- **Server IP:** 47.118.76.93
- **Custom Port:** 3333
- **Username:** taj_uat
- **Password:** t@J@Mba$@$fTp786
- **Remote Directory:** /interface/bills/IN

This script uses the `sftp` command to connect to the remote server and upload the specified files from a local directory to a remote directory. It also uses the sftp batch mode to automate the process without requiring user interaction.

In this example, password of SFTP folder **`(t@J@Mba$@$fTp786)`** contains special character such as `"$"`. The shell is interpreting the `"$"` characters in the password string as the start of a variable name. You can try escaping the special characters with a backslash `(\)`.

```bash
#!/bin/bash

HOST='47.118.76.93'
PORT='3333'
USER='taj_uat'
PASSWORD='t@J@Mba\$@\$fTp786'
REMOTE_DIR='/interface/bills/IN'
LOCAL_DIR='/sftpusers/chroot/sftp-dev-dir/bills'

# Get the 5 latest files in the local directory
latest_files=$(ls -tp $LOCAL_DIR | grep -v / | head -5)

# Copy each of the 5 latest files to the remote directory
for file in $latest_files
do
  expect << EOF
    spawn sftp -oPort=$PORT $USER@$HOST
    expect "password:"
    send "$PASSWORD\r"
    expect "sftp>"
    send "lcd $LOCAL_DIR\r"
    expect "sftp>"
    send "cd $REMOTE_DIR\r"
    expect "sftp>"
    send "put $file\r"
    expect "sftp>"
    send "bye\r"
EOF
done
```

To connect to an SFTP server using a non-standard port (such as port 3333), you can add the `-P` option to the `sftp` command, followed by the port number.

Save the script to a file (e.g., `sftp_script.sh`) and make it executable by running `chmod +x sftp_script.sh`. Finally, run the script by typing `./sftp_script.sh` in your terminal.

This script first gets the 5 latest files in the local directory using the `ls` and `grep` commands, and then loops through each file and copies it to the remote directory using the `expect` script.

Note that this script uses the `sftp` command-line utility, which may not be installed by default on your Ubuntu server. You can install it by running `sudo apt-get install openssh-client` if it is not already installed.

You can try running the script with the `-x` option to enable debugging and see where the error occurs:

```bash
bash -x your_script.sh
```

This will print each command as it is executed, along with any variables that are expanded, which may help you identify the syntax error.
