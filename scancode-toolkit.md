# ScanCode-Toolkit

A typical software project often reuses hundreds of third-party packages. License and packages, dependencies and origin information is not always easy to find and not normalized: ScanCode discovers and normalizes this data for you.

### Installation from Source Code:
You can download the Scancode Toolkit Source Code and build from it yourself.

#### **Download the ScanCode-Toolkit Source Code:**
Run the following once you have Git installed:
```bash
git clone https://github.com/aboutcode-org/scancode-toolkit.git
cd scancode-toolkit
```
#### **Configure the build**
ScanCode use a configure scripts to create an isolated virtual environment, install required packaged dependencies.

**On Linux/Mac:**

- Open a terminal
- cd to the clone directory
- run `./configure`
- run `source venv/bin/activate`

### Run a Scan
Perform a basic scan on the **samples** directory distributed by default with Scancode.

The sample files contain a package `zlib.tar.gz`. So we have to extract the archive before running the scan, to also scan the files inside this package.

To extract the packages inside samples directory:
```bash
extractcode samples
```
Once the package is extracted, you can run a simple scan:
```bash
scancode -clip --json-pp - samples
```

OR with more decided options:
```bash
scancode -clpeui -n 2 --ignore "*.java" --json-pp sample.json samples
```

A Progress report is shown:

```bash
Setup plugins...
Collect file inventory...
Scan files for: info, licenses, copyrights, packages, emails, urls with 2 process(es)...
[####################] 29
Scanning done.
Summary:        info, licenses, copyrights, packages, emails, urls with 2 process(es)
Errors count:   0
Scan Speed:     1.09 files/sec. 40.67 KB/sec.
Initial counts: 49 resource(s): 36 file(s) and 13 directorie(s)
Final counts:   42 resource(s): 29 file(s) and 13 directorie(s) for 1.06 MB
Timings:
  scan_start: 2019-09-24T203514.573671
  scan_end:   2019-09-24T203545.649805
  setup_scan:licenses: 4.30s
  setup: 4.30s
  scan: 26.62s
  total: 31.14s
Removing temporary files...done.
```

### Deciding Scan Options

Some common scan options you should consider using before you start the actual scan, according to your requirements.

1. The basic scan options, i.e. `-c` or `--copyright`, `-l` or `--license`, `-p` or `--package`, `-e` or `--email`, `-u` or `--url`, and `-i` or `--info` can be selected according to your requirements. If you do not need one specific type of information (say, licenses), consider removing it because the more options you scan for, the longer it will take for the scan to complete.

2. `--license-score INTEGER` is to be set if license matching accuracy is desired (Default is 0, and increasing this means a more accurate match). Also, using `--license-text` includes the matched text to the result.

3. `-n INTEGER` option can be used to speed up the scan using multiple parallel processes.

4. `--timeout FLOAT` option can be used to skip files taking a long time to scan.

5. `--ignore <pattern>` can be used to skip certain group of files.

6. `<OUTPUT FORMAT OPTION(s)>` is also a very important decision when you want to use the output for specific tasks/have requirements. Here we are using `json` as ScanCode Workbench imports `json` files only.

7. See more command examples:
    ```bash
    scancode --examples
    ```

You can clone your GitHub repositories into one folder and scan your code using the ScanCode Toolkit. In the following example, we are scanning our GitHub repository using **ScanCode Toolkit** for **licenses** and saving the output in `JSON` format.

```bash
$ cd scancode-toolkit/
$ source venv/bin/activate
$ scancode --license /home/ubuntu/github/demo-code/ --json-pp /home/ubuntu/JSON_Files/demo-code.json
```

If we have hundreds of GitHub repositories cloned in one folder, we can automate the scanning process with the following script:

```bash
#!/bin/bash

# Define the paths
GITHUB_DIR="/home/ubuntu/github"
SCAN_CODE_DIR="/home/ubuntu/scancode-toolkit/scancode-toolkit"
OUTPUT_DIR="/home/ubuntu/JSON_Files"

# Activate the virtual environment
cd "$SCAN_CODE_DIR" || { echo "Failed to change directory to $SCAN_CODE_DIR"; exit 1; }
source venv/bin/activate

# Create output directory if it doesn't exist
mkdir -p "$OUTPUT_DIR"

# Loop through each repository in the GITHUB_DIR
for repo in "$GITHUB_DIR"/*/; do
    # Get the repository name (basename of the directory)
    repo_name=$(basename "$repo")

    # Define the output JSON file path
    output_file="$OUTPUT_DIR/$repo_name.json"

    # Run the scan command
    echo "Scanning $repo_name..."
    scancode --license "$repo" --json-pp "$output_file"

    # Check if the scan was successful
    if [ $? -eq 0 ]; then
        echo "Scan completed for $repo_name. Results saved to $output_file."
    else
        echo "Scan failed for $repo_name."
    fi
done

# Deactivate the virtual environment
deactivate

echo "All scans completed."
```

This script automates the process of scanning multiple GitHub repositories for licenses, making it easier to manage and analyze the results.
