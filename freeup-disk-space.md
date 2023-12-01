### Removing unnecessary packages:
After uninstalling an app there could be packages you don't need anymore. Auto-remove looks for orphaned packages, that is to say packages that you didn't explicitly install yourself (ones marked auto) and that aren't depended on.

```bash
sudo apt-get autoremove
```
### Removing old kernel versions:

Check your current version:

```bash
uname -r
```

See all the old kernels and header files:
```bash
dpkg -l linux-image-* linux-headers-*
```
Remove old kernels using linux-headers:
```bash
sudo apt-get remove linux-image-<XYZ> linux-headers-<XYZ>
```
The `apt-get remove` command supports wildcards, so you can do `apt-get remove linux-image 3.0.* linux-headers-3.0.*` for example, to get rid of many at once.

Make sure you don't kill remove current kernel of course! And maybe keep one or two old version, just in case.


Purge old kernels:

```bash
sudo apt-get purge <old_kernel_package_name>
```
If you don't know which kernel version to remove
```bash
dpkg --get-selections | grep linux-image
```
Check if you have old kernels for deletion
```bash
 ls -lh /boot
```
### Clearing cache and temporary files:

Remove Package cache:
Delete downloaded packages (.deb) already installed (and no longer needed)
```bash
sudo apt-get clean
```

Check content of /var/tmp/:

```bash
du -sh /var/tmp/
```
Remove temporary files:
```bash
sudo rm -rf /tmp/*
```

Check for deborphan:

```bash
man deborphan
```
### Identifying large files or directories:
Use `du` to identify large directories/files:

```bash
du -h /path/to/directory
```
Show top 10 biggest sub-directories in the current directory:
```bash
 du -sk * | sort -nr | head -10
```

List of all installed packages, sorted by size. If you see something big and don't use it - uninstall it
```bash
dpkg-query -W --showformat='${Installed-Size} ${Package}\n' | sort -nr | less
```
Search for big files:
```bash
find / -type f -size +1024k
```
or
```bash
find / -size +50000  -exec ls -lahg {} \;
```

On systemd: Remove the oldest archived journal files until the disk space they use falls below the specified size
```bash
sudo journalctl --vacuum-size 10M
```

### Checking log files:

Log files can sometimes consume significant space. Review and truncate large log files that are no longer needed:

Check for large numbers of log files:

```bash
sudo du -h /var/log
```

Truncate large log files:

```bash
sudo truncate -s 0 /var/log/<log_file_name>
```
### Clearing old package archives:

Remove all stored archives in your cache for packages that can not be downloaded anymore (thus packages that are no longer in the repository or that have a newer version in the repository).
```bash
sudo apt-get autoclean
```

### Checking and removing snap packages:

```bash
sudo snap list
sudo snap remove <package_name>
```

### Removing unwanted files or directories:
Once identified, delete unnecessary large files or directories:

```bash
rm -rf /path/to/directory
```
---
## Cleaner script
This script will execute the biggest chunks on the console:

- Clean apt cache
- Remove config files left from uninstalled .deb packages (happens if you don't use the ``--purge`` switch with `apt-get`)
- Remove every kernel except the one you are using
- Empty the trashes of every user(including root)

Create a file with this content and give it executable rights:

```bash
#!/bin/bash
# Adapted from 71529-ubucleaner.sh - https://web.archive.org/web/20151209182520/http://opendesktop.org/CONTENT/content-files/71529-ubucleaner.sh

OLDCONF=$(dpkg -l|grep "^rc"|awk '{print $2}')
CURKERNEL=$(uname -r|sed 's/-*[a-z]//g'|sed 's/-386//g')
LINUXPKG="linux-(image|headers|ubuntu-modules|restricted-modules)"
METALINUXPKG="linux-(image|headers|restricted-modules)-(generic|i386|server|common|rt|xen)"
OLDKERNELS=$(dpkg -l|awk '{print $2}'|grep -E $LINUXPKG |grep -vE $METALINUXPKG|grep -v $CURKERNEL)
YELLOW="\033[1;33m"; RED="\033[0;31m"; ENDCOLOR="\033[0m"

if [ $USER != root ]; then
  echo -e $RED"Error: must be root! Exiting..."$ENDCOLOR
  exit 0
fi

echo -e $YELLOW"Cleaning apt ..."$ENDCOLOR
aptitude clean
apt-get autoremove
apt-get autoclean

echo -e $YELLOW"Those packages were uninstalled without --purge:"$ENDCOLOR
echo $OLDCONF
#apt-get purge "$OLDCONF"  # fixes the error in the original script
for PKGNAME in $OLDCONF ; do  # a better way to handle errors
  echo -e $YELLOW"Purge package $PKGNAME"
  apt-cache show "$PKGNAME"|grep Description: -A3
  apt-get -y purge "$PKGNAME"
done

echo -e $YELLOW"Removing old kernels..."$ENDCOLOR
echo current kernel you are using:
uname -a
aptitude purge $OLDKERNELS

echo -e $YELLOW"Emptying every trashes..."$ENDCOLOR
rm -rf /home/*/.local/share/Trash/*/** &> /dev/null
rm -rf /root/.local/share/Trash/*/** &> /dev/null

echo -e $YELLOW"Script Finished!"$ENDCOLOR
```

Adapted from [71529-ubucleaner.sh](https://web.archive.org/web/20151209182520/http://opendesktop.org/CONTENT/content-files/71529-ubucleaner.sh)
