# Steps to Extend the Data Volume on Ubuntu 24.04

## 🔹 Step 1: Verify the new volume size
Run:
```bash
lsblk
```
or
```bash
df -h
```
- `lsblk` shows block devices and partitions.  
- You should see your data volume with the new size (e.g., 500GB instead of 300GB).  
- `df -h` shows mounted filesystems and their usage.

---

## 🔹 Step 2: Identify the partition
Check which partition is mounted for your data volume:
```bash
mount | grep <your-mount-point>
```
Example: if your data volume is mounted at `/data`, find the device (e.g., `/dev/xvdf1`).

---

## 🔹 Step 3: Grow the partition (if needed)
If your volume has a partition (like `/dev/xvdf1`), you need to resize it:

```bash
sudo growpart /dev/xvdf 1
```
- `growpart` comes from the `cloud-guest-utils` package. Install if missing:
  ```bash
  sudo apt-get update && sudo apt-get install cloud-guest-utils -y
  ```

This extends partition 1 to fill the new volume size.

---

## 🔹 Step 4: Resize the filesystem
Now extend the filesystem to use the expanded partition:

- For **ext4**:
  ```bash
  sudo resize2fs /dev/xvdf1
  ```

- For **xfs**:
  ```bash
  sudo xfs_growfs <mount-point>
  ```
  Example:
  ```bash
  sudo xfs_growfs /data
  ```

---

## 🔹 Step 5: Verify
Check the new size:
```bash
df -h
```
