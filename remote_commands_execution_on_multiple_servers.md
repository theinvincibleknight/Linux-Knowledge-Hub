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
172.16.90.25
```

#### **`ssh_keys.txt`** – Corresponding SSH commands (one per line).
```
ssh -i /home/ubuntu/AWS_KPs/DEV_BK_KY.pem ubuntu@172.16.90.25
ssh -i /home/ubuntu/AWS_KPs/DEV_DE_KY.pem ubuntu@172.16.90.38
```

### **Step 3: Create `run_commands.sh` Script**
```bash
#!/bin/bash

servers_file="servers.txt"
ssh_keys_file="ssh_keys.txt"
output_file="output.txt"
command="hostname; cat /etc/os-release | grep -i PRETTY; curl -s ipconfig.io;"

echo "" > "$output_file"

# Read servers and SSH keys into arrays
mapfile -t servers < "$servers_file"
mapfile -t ssh_keys < "$ssh_keys_file"

# Loop through both arrays
for i in "${!servers[@]}"; do
    echo "Executing: ${ssh_keys[i]}" | tee -a "$output_file"
    ${ssh_keys[i]} "$command" >> "$output_file" 2>&1
    echo "" | tee -a "$output_file"
done

echo "All commands executed. Output saved in $output_file."
```

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

## **Enhancements**
You can modify the script for:

✅ **Parallel execution** using `xargs` or `GNU parallel`  
✅ **Error handling** for failed SSH connections  
✅ **Logging** with timestamps  

---

## **Contribute**
If you improve this script or add features, feel free to **fork** and create a **pull request**!

---
