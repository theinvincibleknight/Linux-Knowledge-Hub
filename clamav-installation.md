# Installation and Usage of CLAMAV

ClamAV is an open source (GPLv2) anti-virus toolkit, designed especially for e-mail scanning on mail gateways. It provides a number of utilities including a flexible and scalable multi-threaded daemon, a command line scanner and advanced tool for automatic database updates. The core of the package is an anti-virus engine available in a form of shared library.

## Install CLAMAV:
Install the CLAMAV antivirus software using the following command:

```bash
sudo apt update
sudo apt install clamav
sudo apt install clamav-daemon
```

## Updating ClamAV Virus Database:
After installing CLAMAV, it's important to update its virus definition database to ensure it can detect the latest threats:

```bash
sudo freshclam
```
`Freshclam` is a tool specifically designed for this task. Simply calling it on the command line will update the database.

### **Errors:**

```bash
ubuntu@ip-172-31-45-53:~$ sudo freshclam
ERROR: /var/log/clamav/freshclam.log is locked by another process
ERROR: Problem with internal logger (UpdateLogFile = /var/log/clamav/freshclam.log).
ERROR: initialize: libfreshclam init failed.
ERROR: Initialization error!
```

You are getting above error, because you don't have to run it (`sudo freshclam`) manually because it has been run automatically and is running in the background, that's why you receive that message.

If you want to stop the daemon and run it manually:

```bash
sudo systemctl stop clamav-freshclam.service
```
Run it manually:

```bash
sudo freshclam
```

Once updated you should be able to see something like this:

```bash
Sat Aug 26 08:52:44 2023 -> ClamAV update process started at Sat Aug 26 08:52:44 2023
Sat Aug 26 08:52:44 2023 -> daily.cvd database is up-to-date (version: 27012, sigs: 2040022, f-level: 90, builder: raynman)
Sat Aug 26 08:52:44 2023 -> main.cvd database is up-to-date (version: 62, sigs: 6647427, f-level: 90, builder: sigmgr)
Sat Aug 26 08:52:44 2023 -> bytecode.cvd database is up-to-date (version: 334, sigs: 91, f-level: 90, builder: anvilleg)
```
<blockquote>

### What is happening and how to handle it?

Every time when you encounter into a similar situations, errors like `file x has been locked` or `Another process is using this file : /path/to/x` you can use the `lsof` command to find out which process is using that file, in your case if you run:

```bash
sudo lsof /var/log/clamav/freshclam.log
```

You should get an output like:

```bash
COMMAND   PID   USER   FD   TYPE DEVICE SIZE/OFF     NODE NAME
abc       126   user   3wW  REG  259,1  100          1048 /var/log/clamav/freshclam.log
```

The `abc` is the name of process which is using that file, in your case it's: `freshclam`.

That means `freshclam` which you want to run has been already ran by clamav daemons.

you can use `less /var/log/clamav/freshclam.log` or similar commands to see what's going on.

So you don't have to run it manually anymore, it's a process to avoid any conflict and having multiple instance of a same process doing same thing at the same time.

If you want to make it stop and run it manually, then send a `SIGTERM` to its process, that gives the process a chance to finish its job and close itself cleanly, something like:

```bash
sudo pkill -15 -x freshclam
```
- _In this case `sudo` may be necessary_.
- _15: SIGTERM is the default_

Then run it manually:

```bash
sudo freshclam
```
However in this case you can use:

```bash
sudo systemctl stop clamav-freshclam.service
```
to stop the daemon.

</blockquote>

<blockquote>

[OPTIONAL]

You can manually update the virus signature by downloading the `daily.cvd` from clamav database and copying it in `/var/lib/clamav`.

```bash
sudo wget https://database.clamav.net/daily.cvd
sudo cp daily.cvd /var/lib/clamav/
```

</blockquote>

ClamAV team claims they update the virus signature database approximately twice a day. If you do not update the signature you can be vulnarable to new malware and zero-day exploits.

The easiest way to keep the signature updated is to use the clamAV freshclam service.
Start the service and enable it at boot.

To start the service:

```bash
sudo systemctl start clamav-freshclam.service
```

To enable the service:

```bash
sudo systemctl enable clamav-freshclam.service
```

To check the status of service:

```bash
sudo systemctl status clamav-freshclam.service
```
Alternatively you can set fresh clam to run whenever you like as a cron job.

### Scan files and Directories

ClamAV comes with a command line scanning tool called clamscan.
If you want to scan a single file, simply pass it as a argument to the clamscan utility.

```bash
sudo clamscan <path-to-file>
```

Once scan completes, you will be presented with a scan summary.

```bash
/home/ubuntu/banner.sh: OK

----------- SCAN SUMMARY -----------
Known viruses: 8671870
Engine version: 0.103.9
Scanned directories: 0
Scanned files: 1
Infected files: 0
Data scanned: 0.00 MB
Data read: 0.00 MB (ratio 0.00:1)
Time: 33.125 sec (0 m 33 s)
Start Date: 2023:08:26 09:15:10
End Date:   2023:08:26 09:15:43
```

You can see clamscan shows the `OK` message.

[OPTIONAL]
Just for testing purpose, you can run the following command to create an EICAR test file. EICAR (European Expert Group for IT-Security) provides a safe and widely accepted test file for checking the effectiveness of antivirus software without the risk of using real malware.

```bash
echo "X5O!P%@AP[4\PZX54(P^)7CC)7}$EICAR-STANDARD-ANTIVIRUS-TEST-FILE!$H+H*" > testfile.txt
```

This will help familirize ourselves with what to expect if there is a virus detected. Once the file is downloaded simply pass the filename as an argument to clamscan and see what happens.

```bash
clamscan testfile.txt
```
CLAMAV should detect the EICAR test file as a potential threat, confirming that it's working correctly.

Once scan completes, you will be presented with a scan summary.

```bash
/home/ubuntu/testfile.txt: Eicar-Signature FOUND

----------- SCAN SUMMARY -----------
Known viruses: 8671870
Engine version: 0.103.9
Scanned directories: 0
Scanned files: 1
Infected files: 1
Data scanned: 0.00 MB
Data read: 0.00 MB (ratio 0.00:1)
Time: 30.026 sec (0 m 30 s)
Start Date: 2023:08:26 09:22:11
End Date:   2023:08:26 09:22:41
```

You can see clamscan now shows a `FOUND` message insted of the `OK` message. It also shows 1 infected file in scan summary. But no action taken against the file because we did not specify it on the command line.

Same goes for scanning the directory, insted of single filename pass the directory path.

```bash
sudo clamscan <path-to-directory>
```

In order to scan the directory and all its subdirectories we need to pass the recursive flag (`-r`).

```bash
sudo clamscan -r <path-to-directory>
```

Run a simple scan on entire home directory

```bash
sudo clamscan -i -r /home/ubuntu
```

Here we are using `-i` option to limit the output to only infected files.

### Taking action of infected files

When we find a infected file, we can either move the file to directory of our choice. Or copy the file somewhere for further analysis Or remove the file.

Any action taken on infected file can have adverse effect, you can move the file into a quarantine directory renaming it and ensuring it does not have execute permissions is safest for inexperienced users.

- To copy the infected file to another directory we will use the `copy` option.

```bash
sudo clamscan -r --copy=<path-to-destination> <path-to-infected-file-directory>
```

You should be able to see following lines in just above `scan summary`.

```bash
/home/ubuntu/testfile.txt: Eicar-Signature FOUND
/home/ubuntu/testfile.txt: copied to '/home/ubuntu/quarntine/testfile.txt'

----------- SCAN SUMMARY -----------
Known viruses: 8671870
Engine version: 0.103.9
Scanned directories: 0
Scanned files: 1
Infected files: 1
Data scanned: 0.00 MB
Data read: 0.00 MB (ratio 0.00:1)
Time: 29.787 sec (0 m 29 s)
Start Date: 2023:08:26 09:48:06
End Date:   2023:08:26 09:48:35
```

- To move the infected file to another directory we will use the `move` option.

```bash
sudo clamscan -r --move=<path-to-destination> <path-to-infected-file-directory>
```

- To remove the infected file from the directory, we will use the `remove` option.

```bash
sudo clamscan -r --remove=yes <path-to-infected-file-directory>
```
**Output:**

```bash
/home/ubuntu/testfile.txt: Eicar-Signature FOUND
/home/ubuntu/testfile.txt: Removed.

----------- SCAN SUMMARY -----------
Known viruses: 8671870
Engine version: 0.103.9
Scanned directories: 0
Scanned files: 1
Infected files: 1
Data scanned: 0.00 MB
Data read: 0.00 MB (ratio 0.00:1)
Time: 30.177 sec (0 m 30 s)
Start Date: 2023:08:26 09:53:44
End Date:   2023:08:26 09:54:15
```

## Set ClamAV to scan automatically using crontab

https://www.techrepublic.com/article/how-to-install-and-use-clamav-on-ubuntu-server-20-04/
