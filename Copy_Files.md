## Bash Script to copy only latest files from source to destination.

**Practical Use Case Scenario:**
- Only files with the string 'report' in the filename should be copied from source to destination.
- Update the destination file only if it's older than the source file. Don't rewrite every time.
- Only the latest files should be copied from source to destination.
- Need to set a cron job to run this bash script at a specific time

To copy multiple latest files from one directory to another in Ubuntu, you can use the **`find`** command with the **`sort`** and **`tail`** commands to locate and select the latest files based on their modification time.

Here's an example shell script that copies the seven latest files with the filname including string **`'report'`** from the **`/source_directory`** to the **`/destination_directory`**:

```bash
#!/bin/bash

source_dir="/source_directory"
dest_dir="/destination_directory"

cd $source_dir

# Find the seven files with string 'report' in filename and copy them to the destination directory
find . -maxdepth 1 -type f -name "*report*" -printf "%T@\t%p\n" | sort -n | tail -7 | cut -f2- | xargs -I {} cp -u {} $dest_dir
```
**Explanation:**

- **`source_dir`** and **`dest_dir`** are the source and destination directories, respectively.
- **`cd $source_dir`** changes the current working directory to the source directory.
- **`find . -maxdepth 1 -type f -name "*report*" -printf "%T@\t%p\n"`** finds all files in the current directory **`(.)`** with the string **`report`** filename **`(-name "report")`** and prints their modification time in seconds since the epoch **`(%T@)`** followed by the file path **`(%p)`** with a tab character in between **`(\t)`**. The output is sorted by modification time using the **`sort`** -n command.
- **`tail -7`** selects the seven latest files from the sorted list.
- **`cut -f2-`** removes the modification time from the output, leaving only the file path.
- **`xargs -I {} cp -u {} $dest_dir`** copies each selected file to the destination directory using the **`cp`** command with the **`-u`** option, which updates the destination file only if it's older than the source file. This option ensures that files that already exist in the destination directory and are newer than the corresponding files in the source directory are not overwritten. The **`{}`** placeholder is replaced with each file path.

Provide `execute` permission to the bash script:

```bash
chmod +x bash_script.sh
```

To run a bash script on a particular time daily in Linux, you can use the **`cron`** job scheduler. The **`cron`** daemon runs scheduled tasks at specific intervals, including once a day.

Here are the steps to schedule a bash script to run daily using **`cron`**:

1. Open the **`crontab`** file using the following command:
```bash
crontab -e
```

2. To schedule the script to run at a specific time, modify the time and date fields accordingly and add a new line in crontab. For example, to run the script every day at 5:30 AM, use the following syntax:
```bash
30 5 * * * /path/to/script.sh
```
Replace **`/path/to/script.sh`** with the actual path to your bash script. The five asterisks represent the time and date fields for the **`cron`** job.

The first field is for minutes, the second field is for hours, and the remaining three fields are for the day of the month, month, and day of the week, respectively. Use an asterisk to indicate any value.

3. Save the changes to the **`crontab`** file and exit the editor.

Test the bash script by manually running it once, before setting the cron job. Check whether the recent files moved from source to destination as expected. Run it again and check whether it's creating any duplicate files. Once you are confident, that it's running properly, set the cron job.

**Bonus Tip:**

To `cd` to directory with spaces in pathname in bash script:

```bash
x="test\ me"  
eval cd $x
```

