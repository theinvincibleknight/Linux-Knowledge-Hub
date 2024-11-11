# Scanning GitHub Repositories for a Particular String

Steps to scan all branches of GitHub repositories for a specific string, using GitHub's API, cloning repositories, and executing search scripts.

## 1. Fetch the Names of All GitHub Repositories

### 1.1 Create a GitHub Token
Before you can access the repositories, you need to create a personal access token in GitHub. This token will allow you to authenticate and interact with the GitHub API.

1. Go to your GitHub account settings.
2. Navigate to **Developer settings** > **Personal access tokens**.
3. Click on **Generate new token** and select the appropriate scopes (e.g., `repo` for full control of private repositories).
4. Copy the generated token.

### 1.2 Fetch Repository Names
Run the following command to fetch all the GitHub repository names associated with your organization:

```bash
curl -s -L -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  "https://api.github.com/orgs/Admin-Air/repos?type=all&per_page=100&page=1" | \
  grep '"full_name"' | sed 's/.*"full_name": "\(.*\)".*/\1/'
```

If repositories are more than 100, you can use `per_page=200&page=2`

```bash
curl -s -L -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  "https://api.github.com/orgs/Admin-Air/repos?type=all&per_page=200&page=2" | \
  grep '"full_name"' | sed 's/.*"full_name": "\(.*\)".*/\1/'
```

### 1.3 Output Format
The command will output the repository names in the following format:

```
Admin-Air/wp-dw-docs
Admin-Air/wp-dr-webservice
Admin-Air/wp-pax-app
Admin-Air/wp-pipelines
Admin-Air/wp-events-webservice
Admin-Air/wp-info-webservice
Admin-Air/push-biometric-data
```

### 1.4 Save Repository Names
You can remove the organization name "Admin-Air/" from the output to keep only the repository names. Save the cleaned list in a file named `repos.txt`.

## 2. Clone Repositories

### 2.1 Create a Temporary Directory
Create a directory named `temp-code` where you will clone your repositories:

```bash
mkdir temp-code
```

### 2.2 Clone Repositories Script
Create a script named `git-clone.sh` to automate the cloning process:

```bash
vi git-clone.sh
```

Add the following content to the script:

```bash
#!/bin/bash

# Directory where you want to clone the repositories
TARGET_DIR="./temp-code"

# Create the target directory if it doesn't exist
mkdir -p "$TARGET_DIR"

# Change to the target directory
cd "$TARGET_DIR" || exit

# Read the repository names from the text file and clone them
while IFS= read -r repo_name; do
    echo "Cloning repository: $repo_name"
    git clone "git@github.com:Admin-Air/$repo_name.git"
done < ../repos.txt
```

### 2.3 Run the Cloning Script
Execute the script to clone all the repositories into the `temp-code` directory:

```bash
bash git-clone.sh
```

### 2.4 Verify Cloning
After running the script, you can verify that all repositories have been cloned successfully:

```bash
ubuntu@AWDEPLYAUL01:~$ cd temp-code/
ubuntu@AWDEPLYAUL01:~/temp-code$ ll
total 564
drwxrwxr-x 136 ubuntu ubuntu  4096 Oct  1 06:03 ./
drwxr-x---  14 ubuntu ubuntu  4096 Oct  1 07:49 ../
drwxrwxr-x   8 ubuntu ubuntu  4096 Sep 30 16:01 wp-dw-docs/
drwxrwxr-x   3 ubuntu ubuntu  4096 Sep 30 16:01 wp-dr-webservice/
drwxrwxr-x   6 ubuntu ubuntu  4096 Sep 30 16:01 wp-pax-app/
drwxrwxr-x  22 ubuntu ubuntu  4096 Sep 30 16:01 wp-pipelines/
drwxrwxr-x   3 ubuntu ubuntu  4096 Sep 30 16:05 wp-events-webservice/
drwxrwxr-x   4 ubuntu ubuntu  4096 Sep 30 16:05 wp-info-webservice/
drwxrwxr-x   6 ubuntu ubuntu  4096 Sep 30 16:05 push-biometric-data/
```

## 3. Search for a Particular String

### 3.1 Create Search Script
Inside the `temp-code` directory, create a script named `search-string.sh` to search for a specific string (e.g., "password"):

```bash
vi search-string.sh
```

Add the following content to the script:

```bash
#!/bin/bash

git fetch --all

for branch in $(git branch -r | awk '{print $1}'); do
    echo "#########################"
    echo "Branch: $branch"
    git grep -i "password" $(git rev-parse $branch)
done
```

- The script fetches all recent changes from the remote repository.
- It loops through all branches of the current repository and searches for the word "password" in each branch.
- The output is displayed in the terminal.

### 3.2 Copy and Run the Search Script
To copy the search script into each repository and execute it, create another script named `copy-run-script.sh`:

```bash
vi copy-run-script.sh
```

Add the following content:

```bash
#!/bin/bash

# Path to the script you want to copy
SCRIPT_NAME="search-string.sh"

# Loop through each directory in the current directory
for dir in */; do
    # Remove the trailing slash from the directory name
    dir_name="${dir%/}"

    # Copy the script into the directory
    cp "$SCRIPT_NAME" "$dir_name"

    # Run the script and redirect output to a .txt file named after the directory
    (cd "$dir_name" && bash "$SCRIPT_NAME" > "/home/ubuntu/password_output/${dir_name}.txt")
done
```

### 3.3 Explanation of the Copy and Run Script
- This script loops through each cloned repository directory.
- It copies the `search-string.sh` script into each repository.
- It then executes the script within the context of that repository and redirects the output to a text file named after the repository in the specified output directory (`/home/ubuntu/password_output/`).

### 3.4 Run the Copy and Run Script
Execute the script to copy the search script into each repository and run it:

```bash
bash copy-run-script.sh
```

### 3.5 Check the Output
After running the script, navigate to the output directory to check the results:

```bash
cd /home/ubuntu/password_output/
ls
```

You should see a `.txt` file for each repository containing the search results.

## 4. Search for a String in All Output Files

If you want to search for a particular string (e.g., "password") across all the output files generated, you can use the `grep` command:

```bash
grep -i "password" *.txt > password_found.txt
```

- This command searches for the string "password" (case insensitive due to the `-i` option) in all `.txt` files in the current directory.
- The results are redirected to a file named `password_found.txt`, which will contain all occurrences of the string across the output files.

---

### Step 3 Alternative:

After Step 2 (cloning the GitHub repository), if you want to search for a particular string, you can use the following script. Copy the script in the cloned repository directory.

```bash
vi code-locate-string.sh
```

```bash
#!/bin/bash
# This script must be run in the same directory that contains all the cloned repositories.
#
# Function to search for "password" in all remote branches of a Git repository
search_in_git_repo() {
    # Fetch the latest branches and commits
    git fetch --all

    # Iterate over each remote branch
    for branch in $(git branch -r | awk '{print $1}'); do
        # Strip the remote name (e.g., 'origin/')
        branch_name=${branch#origin/}

        echo "#########################"
        echo "Branch: $branch_name"

        # Perform the grep search
        results=$(git grep -i "password" $(git rev-parse "$branch") | grep -vi "passwords" | grep -vi "ipassword" | grep -vi "keypassword")

        # Check if results were found and output accordingly
        if [ -n "$results" ]; then
            echo "Path: $PWD Branch: $branch_name" >> ../results.log
            echo "$results" >> ../results.log
            echo "$results"
        else
            echo "No matches found."
        fi
    done
}

# Iterate through all directories in the current directory
for dir in */; do
    echo "Entering directory: $dir"

    cd "$dir" || continue  # Change to the directory or skip if it fails

    # Check if it is a Git repository
    if [ -d .git ]; then
        echo "Found Git repository in: $dir"
        search_in_git_repo
    else
        echo "No Git repository found in: $dir"
    fi

    cd ..  # Return to the parent directory
done
```

This script searches for the string "password" and uses `grep -vi` to ignore other strings that contain "password" in them. And saves the output **results.log** in the same directory.

