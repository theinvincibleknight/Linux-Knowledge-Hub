## To set up file synchronization between two Ubuntu servers using `lsyncd`, you can follow these steps:

**Server Details:**
1. SFTPAPP01 (10.16.6.1)
2. SFTPAPP02 (10.16.6.2)

**1. Install `lsyncd`:**

First, you need to install `lsyncd` on both servers.

```bash
sudo apt update
sudo apt install lsyncd
```
**2. Create SSH Keys (if not already created):**

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

**3. Configure lsyncd:**

Create a configuration file for lsyncd on each server. Let's assume you create a file named `lsyncd.conf` in `/etc/lsyncd/`.

On **SFTPAPP01**:

```bash
sudo vi /etc/lsyncd/lsyncd.conf
```
Add the following content:

```lua
settings {
    statusFile = "/var/log/lsyncd/lsyncd.status",
    statusInterval = 10
}

sync {
    default.rsyncssh,
    source = "/sftpusers/files/",
    host = "10.16.6.2",
    targetdir = "/data/syncfiles/",
    delay = 5,
    excludeFrom = "/etc/lsyncd/lsyncd-excludes.txt",
}
```
On **SFTPAPP02**:

```bash
sudo vi /etc/lsyncd/lsyncd.conf
```
Add the following content:

```lua
settings {
    statusFile = "/var/log/lsyncd/lsyncd.status",
    statusInterval = 10
}

sync {
    default.rsyncssh,
    source = "/data/syncfiles/",
    host = "10.16.6.1",
    targetdir = "/sftpusers/files/",
    delay = 5,
    excludeFrom = "/etc/lsyncd/lsyncd-excludes.txt",
}
```

**4. Start lsyncd:**

After configuring lsyncd, start the lsyncd service on both servers.

```bash
sudo systemctl start lsyncd
```

You can also enable lsyncd to start automatically on system boot:

```bash
sudo systemctl enable lsyncd
```
**5. Monitor lsyncd:**

You can monitor the synchronization process and check for any errors in the log files specified in the configuration (`/var/log/lsyncd/lsyncd.status`).

```bash
tail -f /var/log/lsyncd/lsyncd.status
```

With these steps, you should have lsyncd set up to synchronize files between the specified directories on both servers. Make sure to test the synchronization and monitor it regularly to ensure it's working as expected.
