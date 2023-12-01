To achieve synchronization between two directories, you can create a bash script that utilizes `rsync` to synchronize two folders and run it periodically using a cron job. Here's a sample script:

### Create the bash script:

```bash
#!/bin/bash

# Source and destination folders
source_folder="/path/to/source_folder"
destination_folder="/path/to/destination_folder"

# Run rsync to sync the folders
rsync -av --delete "$source_folder/" "$destination_folder/"

echo "Folders synchronized at $(date)"
```
<blockquote>

**Explaination:**

- `rsync`: This is the command itself, used for file and directory synchronization.

- `-av`: These are options passed to `rsync`:

    - `-a`: Archive mode. It tells `rsync` to preserve various attributes of the files and directories, such as permissions, ownership, timestamps, etc. It's a shorthand for several options combined.

    - `-v`: Verbose mode. It displays detailed information about the files being transferred.

- `--delete`: This option tells `rsync` to delete any files in the destination folder that do not exist in the source folder. It ensures that the destination folder is an exact copy of the source folder.

- `"$source_folder"`: This is the source folder whose contents you want to synchronize. Replace it with the actual path to your source folder.

- `"$destination_folder"`: This is the destination folder where you want to copy the contents of the source folder. Replace it with the actual path to your destination folder.

When you run this `rsync` command, it will compare the contents of the source and destination folders and make sure that the destination folder matches the source folder, including any additions, updates, or deletions. The `-av` options ensure detailed output, so you can see what `rsync` is doing during the synchronization process.
</blockquote>

Make sure to replace `/path/to/source_folder` and `/path/to/destination_folder` with the actual paths to your source and destination folders.

### Make the script executable:
```bash
chmod +x sync_folders.sh
```
### Set up a cron job to run the script every 2 hours:
```bash
crontab -e
```
Add the following line to run the script every 2 hours:

```bash
0 */2 * * * /path/to/sync_folders.sh
```
Again, `replace /path/to/sync_folders.sh` with the actual path to your script.

This will periodically check and synchronize the two folders, ensuring that duplicate files are not copied, and you'll see a log entry each time the folders are synchronized.
