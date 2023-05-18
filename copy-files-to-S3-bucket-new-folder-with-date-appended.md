# Bash script to create new folder in S3 bucket with current date and time and copy the files from AWS ubuntu server to that S3 bucket.

To create a new folder in an S3 bucket with the current date and time and copy files from an AWS Ubuntu server to that S3 bucket, you can use a Bash script with the following steps:

- Get the current date and time in a specific format.
- Create a new folder in the S3 bucket using the current date and time as the folder name.
- Copy files from the AWS Ubuntu server to the newly created folder in the S3 bucket.

Here is an example Bash script to achieve this:

```bash
#!/bin/bash

# Get the current date and time in a specific format
current_datetime=$(date +"%Y-%m-%d_%H-%M-%S")

# Set your S3 bucket name
s3_bucket_name="your-s3-bucket-name"

# Create a new folder in the S3 bucket using the current date and time as the folder name
aws s3api put-object --bucket $s3_bucket_name --key "$current_datetime/"

# Copy files from the AWS Ubuntu server to the newly created folder in the S3 bucket
aws s3 cp /path/to/local/folder/ s3://$s3_bucket_name/$current_datetime/ --recursive
```

Make sure to replace `your-s3-bucket-name` with the actual name of your S3 bucket, and `/path/to/local/folder/` with the actual path to the local folder containing the files you want to copy. Save this script with a `.sh` extension, and then execute it using the following command:

```bash
$ bash script-name.sh
```

This will create a new folder in your S3 bucket with the current date and time as the folder name, and then copy the files from the local folder to that newly created folder.
