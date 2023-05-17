# Choose a filename from the source filename and append the current date and time to it when copying files from an AWS Ubuntu server to an S3 bucket.

To choose a filename from the source filename and append the current date and time to it when copying files from an AWS Ubuntu server to an S3 bucket, you can use a Bash script with the following steps:

- Get the current date and time in a specific format.
- Extract the filename from the source path.
- Append the current date and time to the filename.
- Copy the file from the AWS Ubuntu server to the S3 bucket using the new filename.

Here is an example Bash script to achieve this:

```bash
#!/bin/bash

# Set your S3 bucket name
s3_bucket_name="your-s3-bucket-name"

# Get the current date and time in a specific format
current_date_time=$(date +"%Y-%m-%d_%H-%M-%S")

# Set the source file path
source_file_path="/path/to/local/folder/filename.txt"

# Extract the filename from the source path
filename=$(basename "$source_file_path")

# Append the current date and time to the filename
new_filename="${filename%.*}_${current_date_time}.${filename##*.}"

# Copy the file from the AWS Ubuntu server to the S3 bucket using the new filename
aws s3 cp "$source_file_path" s3://$s3_bucket_name/"$new_filename"
```
Make sure to replace `your-s3-bucket-name` with the actual name of your S3 bucket, and `/path/to/local/folder/filename.txt` with the actual path to the local file you want to copy.

In this example, the script extracts the filename from the source path `/path/to/local/folder/filename.txt`, and appends the current date and time in the format of `YYYY-MM-DD_HH-MM-SS` to it. This creates a new filename in the format of `filename_YYYY-MM-DD_HH-MM-SS.txt`. The script then copies the file from the AWS Ubuntu server to the S3 bucket using the new filename.

Save this script with a `.sh` extension, and then execute it using the following command:

```bash
$ bash script-name.sh
```
This will copy the file from the AWS Ubuntu server to the S3 bucket with the current date and time appended to the filename. If you want to copy multiple files, you can include multiple copy commands in the script with different source paths and new filenames.
