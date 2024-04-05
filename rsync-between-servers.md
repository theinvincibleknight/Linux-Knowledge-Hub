## To set up file synchronization between two Ubuntu servers using `rsync`, you can follow these steps:

**Server Details:**
1. SFTPAPP01 (10.16.6.1)
2. SFTPAPP02 (10.16.6.2)

**1. Install `rsync`:**

First, you need to install `rsync` on both servers.

```bash
sudo apt update
sudo apt install rsync
```

**2. Configure SSH Keys (if not already done):**

If you haven't already set up SSH keys between the two servers, you need to generate them and exchange them between the servers.

On **SFTPAPP01**:

```bash
ssh-keygen -t rsa
```
Then copy the public key to **SFTPAPP02**:

```bash
ssh-copy-id ubuntu@10.16.6.2
```
Repeat this step vice-versa (from **SFTPAPP02** to **SFTPAPP01**) if needed.

**3. Perform the rsync:**

Run the `rsync` command to synchronize the files from **SFTPAPP01** to **SFTPAPP02**.

```bash
rsync -avz --progress --delete /sftpusers/files/ user@10.16.6.2:/data/syncfiles/
```

This command will synchronize the files from `/sftpusers/files/` on **SFTPAPP01** to `/data/syncfiles/` on **SFTPAPP02**. The `--delete` option ensures that any files deleted from the source are also deleted on the destination.

[Optional] If you want to sync files from **SFTPAPP02** to **SFTPAPP01**, you can reverse the source and destination in the command.

```bash
rsync -avz --progress --delete user@10.16.6.2:/data/syncfiles/ /sftpusers/files/
```
**4. Automate the process (optional):**

You can create a script to run the `rsync` command and schedule it to run periodically using `cron` if you want the synchronization to be automated.

For example, create a script named `sync_files.sh`:

```bash
#!/bin/bash
rsync -avz --progress --delete /sftpusers/files/ ubuntu@10.16.6.2:/data/syncfiles/
```
Make the script executable:

```bash
chmod +x sync_files.sh
```
Then add a `cron` job to run the script at your desired interval. For example, to run the script every hour, you can add the following line to your crontab using `crontab -e`:

```bash
0 * * * * /home/ubuntu/scripts/sync_files.sh
```

With these steps, you should have rsync set up to synchronize files between the specified directories on both servers. Make sure to test the synchronization and monitor it regularly to ensure it's working as expected.
