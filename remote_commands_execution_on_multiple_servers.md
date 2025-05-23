# **Automating Remote Commands Execution via SSH on Multiple Servers**

## **Overview**
This guide provides a **dynamic Bash script** that allows users to remotely execute commands on multiple servers from a bastion host.  
Users can **store server IPs and SSH authentication details in separate files**, ensuring a scalable and flexible approach.

## **Prerequisites**
Before using this script, ensure:
- You have SSH access to target servers.
- Each server has a corresponding private key for authentication.
- The bastion host has network connectivity to all target servers.
- `bash` is installed (the script won't work in `sh`).
- `curl` is installed for fetching public IPs.

---

## **Step-by-Step Setup**

### **Step 1: Clone the Repository**
```bash
git clone https://github.com/your-repo-name.git
cd your-repo-name
```

### **Step 2: Create Required Files**
#### **`servers.txt`** – List of server IPs (one per line).
```
172.16.90.38
10.16.90.25
```

#### **`ssh_keys.txt`** – Corresponding SSH commands (one per line).
```
ssh -i /home/ubuntu/AWS_KPs/DEV_BK_KY.pem ubuntu@172.16.90.25
ssh -i /home/ubuntu/AWS_KPs/DEV_DE_KY.pem ubuntu@172.16.90.38
ssh -i /home/ubuntu/AWS_KPs/Prod_DE_KY.pem ubuntu@10.16.90.38
ssh -i /home/ubuntu/AWS_KPs/Prod_BK_KY.pem ubuntu@10.16.90.25
```

### **Step 3: Create `run_commands.sh` Script**
```bash
#!/bin/bash

servers_file="servers.txt"
ssh_keys_file="ssh_keys.txt"
output_file="output.txt"
command="hostname; cat /etc/os-release | grep -i PRETTY; curl -s ipconfig.io"

echo "" > "$output_file"

# Read servers into an array
mapfile -t servers < "$servers_file"

# Loop through each server
for server in "${servers[@]}"; do
    # Find the corresponding SSH key for the current server
    ssh_key=$(grep "$server" "$ssh_keys_file")

    if [ -n "$ssh_key" ]; then
        echo "Executing: $ssh_key" | tee -a "$output_file"

        # Execute the command on the server
        output=$($ssh_key "$command" 2>&1)

        # Check if the command was successful
        if [ $? -eq 0 ]; then
            echo "$output" >> "$output_file"
        else
            echo "Error executing command on $server: $output" >> "$output_file"
        fi

        echo "" | tee -a "$output_file"
    else
        echo "No matching SSH key for server $server" | tee -a "$output_file"
    fi
done

echo "All commands executed. Output saved in $output_file."
```

**Script Breakdown**

```bash
#!/bin/bash
```
- This line indicates that the script should be run using the Bash shell.

```bash
servers_file="servers.txt"
ssh_keys_file="ssh_keys.txt"
output_file="output.txt"
command="hostname; cat /etc/os-release | grep -i PRETTY; curl -s ipconfig.io"
```
- These lines define variables for the file names and the command to be executed on each server. 
  - `servers_file`: The file containing the list of server IPs.
  - `ssh_keys_file`: The file containing the SSH commands with keys.
  - `output_file`: The file where the output of the commands will be saved.
  - `command`: The command string that will be executed on each server, which includes fetching the hostname, OS release information, and the public IP address.

```bash
echo "" > "$output_file"
```
- This line clears the contents of `output.txt` (or creates it if it doesn't exist) to ensure that the output from the current run does not mix with previous runs.

```bash
mapfile -t servers < "$servers_file"
```
- This line reads the contents of `servers.txt` into an array called `servers`. Each line in the file becomes an element in the array.

```bash
for server in "${servers[@]}"; do
```
- This line starts a loop that iterates over each server IP in the `servers` array.

```bash
    ssh_key=$(grep "$server" "$ssh_keys_file")
```
- This line uses `grep` to search for the current server IP in the `ssh_keys.txt` file. If a match is found, the corresponding SSH command is stored in the variable `ssh_key`.

```bash
    if [ -n "$ssh_key" ]; then
```
- This line checks if `ssh_key` is not empty (i.e., a matching SSH command was found).

```bash
        echo "Executing: $ssh_key" | tee -a "$output_file"
```
- This line prints the SSH command being executed to the terminal and appends it to `output.txt`.

```bash
        output=$($ssh_key "$command" 2>&1)
```
- This line executes the SSH command stored in `ssh_key` along with the specified command. The output (both standard output and standard error) is captured in the variable `output`.

```bash
        if [ $? -eq 0 ]; then
```
- This line checks the exit status of the last command executed (the SSH command). An exit status of `0` indicates success.

```bash
            echo "$output" >> "$output_file"
```
- If the command was successful, this line appends the output to `output.txt`.

```bash
        else
            echo "Error executing command on $server: $output" >> "$output_file"
```
- If the command failed, this block logs an error message along with the output to `output.txt`.

```bash
        echo "" | tee -a "$output_file"
```
- This line adds a blank line to `output.txt` for better readability.

```bash
    else
        echo "No matching SSH key for server $server" | tee -a "$output_file"
```
- If no matching SSH key was found for the current server, this line logs a message indicating that.

```bash
done
```
- This line marks the end of the loop.

```bash
echo "All commands executed. Output saved in $output_file."
```
- Finally, this line prints a message indicating that all commands have been executed and the output has been saved.

### **Step 4: Make the Script Executable**
```bash
chmod +x run_commands.sh
```

### **Step 5: Run the Script**
```bash
bash run_commands.sh
```

### **Step 6: Check the Output**
```bash
cat output.txt
```
The output will include:
- Hostname of each server
- OS version details
- Public IP of each server
---

## **Summary**
The script automates the process of executing a set of commands on multiple remote servers via SSH. It reads the server IPs and corresponding SSH commands from specified files, executes the commands, and captures the output, logging both successful results and any errors encountered. This makes it a useful tool for managing multiple servers efficiently.

