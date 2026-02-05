
# Steps to Extend the Root Volume on Ubuntu 24.04

## 🔹 Step 1: Free up some space temporarily
When the root filesystem is full, commands like `apt-get` or `growpart` may fail. Free up a little space (at least 200–500 MB):

- Clear package cache:
  ```bash
  sudo apt-get clean
  ```
- Remove old logs:
  ```bash
  sudo journalctl --vacuum-size=100M
  ```
- Delete unnecessary files in `/tmp`:
  ```bash
  sudo rm -rf /tmp/*
  ```
- If you have large files you don’t need, move them to another attached volume or delete them.

---

## 🔹 Step 2: Verify the new volume size
Check that the cloud provider shows the increased size:
```bash
lsblk
```
You should see your root device (e.g., `/dev/xvda` or `/dev/nvme0n1`) with the new size.

---

## 🔹 Step 3: Grow the root partition
If your root volume has a partition (usually `/dev/xvda1` or `/dev/nvme0n1p1`):

1. Install growpart (if not already installed):
   ```bash
   sudo apt-get update && sudo apt-get install cloud-guest-utils -y
   ```
2. Run growpart:
   ```bash
   sudo growpart /dev/xvda 1
   ```
   *(Replace `/dev/xvda` with your actual root device, and `1` with the partition number.)*

---

## 🔹 Step 4: Resize the filesystem
Now extend the filesystem to use the expanded partition:

- If root is **ext4**:
  ```bash
  sudo resize2fs /dev/xvda1
  ```

- If root is **xfs** (common on cloud images):
  ```bash
  sudo xfs_growfs /
  ```

---

## 🔹 Step 5: Verify
Check the new size:
```bash
df -h /
```

---

Example:

```bash
df -hT
lsblk
sudo growpart /dev/nvme0n1 1
lsblk
sudo resize2fs /dev/nvme0n1p1
df -hT
```
