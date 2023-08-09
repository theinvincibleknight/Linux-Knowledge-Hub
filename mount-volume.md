# Create and mount an Amazon EBS to an Ubuntu EC2 instance

## Create an Amazon EBS volume:

- Go to the AWS Management Console and navigate to the EC2 service.
- Select `"Volumes"` from the left-hand menu under `"ELASTIC BLOCK STORE"`.
- Click on `"Create Volume"` and configure the volume settings, such as volume type, size, availability zone, etc.
- Click `"Create"` to create the EBS volume.

## Attach the EBS volume to your EC2 instance:

- Select the newly created volume in the Volumes list.
- Click on `"Actions"` and choose `"Attach Volume"`.
- In the `"Instance"` field, select the EC2 instance to which you want to attach the volume.
- Specify the `"Device"` name **(e.g., `/dev/xvdf`)** to which the volume will be attached.
- Click `"Attach"` to attach the volume to the EC2 instance.

## SSH into your EC2 instance:

- Obtain the public IP address or DNS name of your EC2 instance.
- Open a terminal or command prompt and use the SSH command to connect to your instance. For example:

```bash
ssh -i <path_to_key_pair_file> ec2-user@<public_ip_address>
```

Switch to root user using `sudo su -` or run every command using `sudo` command.

- Now verify if Volume is attached or not by running `lsblk` command.

```bash
root@ip-172-31-45-53:~# lsblk
NAME     MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
loop0      7:0    0  24.4M  1 loop /snap/amazon-ssm-agent/6312
loop1      7:1    0  24.8M  1 loop /snap/amazon-ssm-agent/6563
loop2      7:2    0  63.3M  1 loop
loop3      7:3    0 111.9M  1 loop /snap/lxd/24322
loop4      7:4    0  49.8M  1 loop
loop5      7:5    0  53.2M  1 loop /snap/snapd/19122
loop6      7:6    0  55.6M  1 loop /snap/core18/2745
loop7      7:7    0  63.3M  1 loop /snap/core20/1879
loop8      7:8    0  63.5M  1 loop /snap/core20/1891
loop9      7:9    0  55.6M  1 loop /snap/core18/2751
loop10     7:10   0  53.3M  1 loop /snap/snapd/19361
xvda     202:0    0    20G  0 disk
├─xvda1  202:1    0  19.9G  0 part /
├─xvda14 202:14   0     4M  0 part
└─xvda15 202:15   0   106M  0 part /boot/efi
xvdf     202:80   0   100G  0 disk
root@ip-172-31-45-53:~#
```

- Check if the volume has any data using the `file -s /dev/xvdf` command.
If the above command output shows **`“/dev/xvdf: data“`**, it means your volume is empty.

```bash
root@ip-172-31-45-53:~# file -s /dev/xvdf
/dev/xvdf: data
root@ip-172-31-45-53:~#
```

- Format the volume to the ext4 filesystem using the `mkfs -t ext4 /dev/xvdf` command.
Alternatively, you can also use the xfs format. You have to use either ext4 or xfs.

```bash
root@ip-172-31-45-53:~# mkfs -t ext4 /dev/xvdf
mke2fs 1.46.5 (30-Dec-2021)
Creating filesystem with 26214400 4k blocks and 6553600 inodes
Filesystem UUID: 96d52fb1-7798-4482-9400-430e977ebe92
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
        4096000, 7962624, 11239424, 20480000, 23887872

Allocating group tables: done
Writing inode tables: done
Creating journal (131072 blocks): done
Writing superblocks and filesystem accounting information: done

root@ip-172-31-45-53:~#
```
- Create a directory of your choice to mount our new ext4 volume. I am using the name “newvolume“. You can name it something meaningful to you.

```bash
root@ip-172-31-45-53:/# mkdir newvolume
```

- Mount the volume to “newvolume” directory using the `mount /dev/xvdf /newvolume/` command.

```bash
root@ip-172-31-45-53:/# mount /dev/xvdf /newvolume/
```

- List the size of mounted partition using `df -Th` command

```bash
root@ip-172-31-45-53:/# df -Th
Filesystem     Type   Size  Used Avail Use% Mounted on
/dev/root      ext4    20G  2.5G   17G  13% /
tmpfs          tmpfs  484M     0  484M   0% /dev/shm
tmpfs          tmpfs  194M  880K  193M   1% /run
tmpfs          tmpfs  5.0M     0  5.0M   0% /run/lock
/dev/xvda15    vfat   105M  6.1M   99M   6% /boot/efi
tmpfs          tmpfs   97M  4.0K   97M   1% /run/user/1000
/dev/xvdf      ext4    98G   24K   93G   1% /newvolume
root@ip-172-31-45-53:/#
```

You will be able to see `/newvolume` folder is attached with `/dev/xvdf` volume. Now you can `cd` into the /newvolume and start using it.

```bash
root@ip-172-31-45-53:/# cd newvolume
```

**(Optional)** Use the `lsblk -f` command to get information about all of the devices attached to the instance.
You will be able to see filesystem, available and used size, attached mountpoints, etc.

```bash
root@ip-172-31-45-53:/# lsblk -f
NAME     FSTYPE   FSVER LABEL           UUID                                 FSAVAIL FSUSE% MOUNTPOINTS
loop0    squashfs 4.0                                                              0   100% /snap/amazon-ssm-agent/6312
loop1    squashfs 4.0                                                              0   100% /snap/amazon-ssm-agent/6563
loop2    squashfs 4.0
loop3    squashfs 4.0                                                              0   100% /snap/lxd/24322
loop4    squashfs 4.0
loop5    squashfs 4.0                                                              0   100% /snap/snapd/19122
loop6    squashfs 4.0                                                              0   100% /snap/core18/2745
loop7    squashfs 4.0                                                              0   100% /snap/core20/1879
loop8    squashfs 4.0                                                              0   100% /snap/core20/1891
loop9    squashfs 4.0                                                              0   100% /snap/core18/2751
loop10   squashfs 4.0                                                              0   100% /snap/snapd/19361
xvda
├─xvda1  ext4     1.0   cloudimg-rootfs cda0c0a7-e64a-4413-85a5-a0235f6f567f   16.7G    13% /
├─xvda14
└─xvda15 vfat     FAT32 UEFI            54D6-C8B8                              98.3M     6% /boot/efi
xvdf     ext4     1.0                   96d52fb1-7798-4482-9400-430e977ebe92   92.9G     0% /newvolume
root@ip-172-31-45-53:/#
```

## To mount an attached volume automatically after reboot:

- (Optional) Create a backup of your `/etc/fstab` file that you can use if you accidentally destroy or delete this file while editing it.

```bash
root@ip-172-31-45-53:/# cp /etc/fstab /etc/fstab.orig
```

- Use the **`blkid`** command to find the UUID of the device. Make a note of the UUID of the device that you want to mount after reboot. You'll need it in the following step.

```bash
root@ip-172-31-45-53:/# blkid
/dev/xvda1: LABEL="cloudimg-rootfs" UUID="cda0c0a7-e64a-4413-85a5-a0235f6f567f" BLOCK_SIZE="4096" TYPE="ext4" PARTUUID="b2e4a171-bfec-43f0-928d-f8fe44362f17"
/dev/xvda15: LABEL_FATBOOT="UEFI" LABEL="UEFI" UUID="54D6-C8B8" BLOCK_SIZE="512" TYPE="vfat" PARTUUID="abfe16f0-dfd7-479d-93a7-67821caab591"
/dev/loop1: TYPE="squashfs"
/dev/xvda14: PARTUUID="95e637f7-7ab6-48b4-ac14-66a7518b75ba"
/dev/loop8: TYPE="squashfs"
/dev/loop6: TYPE="squashfs"
/dev/loop4: TYPE="squashfs"
/dev/xvdf: UUID="96d52fb1-7798-4482-9400-430e977ebe92" BLOCK_SIZE="4096" TYPE="ext4"
/dev/loop2: TYPE="squashfs"
/dev/loop0: TYPE="squashfs"
/dev/loop9: TYPE="squashfs"
/dev/loop7: TYPE="squashfs"
/dev/loop5: TYPE="squashfs"
/dev/loop3: TYPE="squashfs"
/dev/loop10: TYPE="squashfs"
root@ip-172-31-45-53:/#
```

For Ubuntu 18.04 use the lsblk command.

```bash
[ec2-user ~]$ sudo lsblk -o +UUID
```

- Open the /etc/fstab file using any text editor, such as `nano` or `vim`.

```bash
root@ip-172-31-45-53:/# vi /etc/fstab
```

- Add the following entry to `/etc/fstab` to mount the device at the specified mount point. The fields are the UUID value returned by `blkid` (or `lsblk` for Ubuntu 18.04), the mount point, the file system, and the recommended file system mount options.

In the following example, we mount the device with UUID `96d52fb1-7798-4482-9400-430e977ebe92` to mount point `/newvolume` and we use the `ext4` file system. We also use the defaults and nofail flags. We specify 0 to prevent the file system from being dumped, and we specify 2 to indicate that it is a another device.

```bash
UUID=96d52fb1-7798-4482-9400-430e977ebe92  /newvolume  ext4  defaults,nofail  0  2
```

> **Note**:
If you ever boot your instance without this volume attached (for example, after moving the volume to another instance), the `nofail` mount option enables the instance to boot even if there are errors mounting the volume. Debian derivatives, including Ubuntu versions earlier than 16.04, must also add the `nobootwait` mount option.

- To verify that your entry works, run the following commands to unmount the device and then mount all file systems in `/etc/fstab`. If there are no errors, the `/etc/fstab` file is OK and your file system will mount automatically after it is rebooted.

```bash
root@ip-172-31-45-53:/# umount /dev/xvdf
root@ip-172-31-45-53:/# mount -a
```

If you receive an error message, address the errors in the file.

> **Warning**:
Errors in the `/etc/fstab` file can render a system unbootable. Do not shut down a system that has errors in the `/etc/fstab` file.

If you are unsure how to correct errors in `/etc/fstab` and you created a backup file in the first step of this procedure, you can restore from your backup file using the following command.

```bash
root@ip-172-31-45-53:/# root@ip-172-31-45-53:/# umount /dev/xvdf
```
## Provide ownership and permissions for a non-root user

To provide ownership and permissions for a non-root user to access a specific folder in Ubuntu, you can follow these steps:

1. **Identify the Folder:**

Make sure you know the exact path of the folder you want to grant access to. For this example, let's assume the folder is located at `/path/to/folder`.

2. **Change Ownership:**

Use the `chown` command to change the ownership of the folder to the non-root user. Replace username with the actual `username` of the non-root user:

```bash
sudo chown username:username /path/to/folder
```

3. **Grant Read and Execute Permissions:**

To grant the non-root user read and execute permissions on the folder, use the `chmod` command:

```bash
sudo chmod +rx /path/to/folder
```

This command adds read and execute permissions to the folder for the owner, group, and others.

4. **Verify Permissions:**

You can verify that the permissions and ownership have been set correctly using the `ls` command:

```bash
ls -l /path/to/folder
```

The output will show the permissions, ownership, and other information.

Now, the non-root user should have ownership of the folder and be able to access its contents. They will also have read and execute permissions on the folder. If the folder contains files that the user needs to modify, you might need to adjust permissions accordingly.
