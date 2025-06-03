# SOE Automation Script

## Overview
This script automates the **Standard Operating Environment (SOE)** setup for Ubuntu systems. It installs essential services, sets up security configurations, and ensures compliance with predefined system policies.

## Features
- Installs **NTP** and configures time synchronization
- Sets the **hostname** dynamically
- Configures a **logon banner** for security warnings
- Enforces **auto-logout** for inactive sessions
- Sets command **history formatting** for better tracking
- Installs **Sophos** and **UEMS agent** for endpoint security
- Logs success and failure details to `/var/log/soe_script.log`

## Script

```bash
#!/bin/bash

log_file="/var/log/soe_script.log"

# Function to log messages
log() {
    echo "$(date '+%Y-%m-%d %H:%M:%S') - $1" | sudo tee -a "$log_file"
}

# Function to install and configure NTP
install_ntp() {
    log "Installing NTP..."
    sudo apt-get update && sudo apt-get install -y ntp
    if [ $? -eq 0 ]; then
        log "NTP installed successfully."
    else
        log "Failed to install NTP."
        return
    fi
    sudo systemctl restart ntp
    log "NTP service restarted."
    sleep 15
}

# Function to set the hostname
set_hostname() {
    read -p "Enter the new hostname: " hostname
    sudo hostnamectl set-hostname "$hostname"
    if [ $? -eq 0 ]; then
        log "Hostname set to $hostname."
    else
        log "Failed to set hostname."
    fi
    sleep 15
}

# Function to set the logon banner
set_logon_banner() {
    log "Setting logon banner..."
    sudo bash -c 'cat <<EOF > /etc/update-motd.d/99-company-banner
#!/bin/sh
echo "********************************************************************************"
echo "A C C E S S   W A R N I N G!!!!!"
echo "This is a private computer system. Unauthorized access or use is prohibited and only authorized users are permitted. Use of this system constitutes consent to monitoring at all times and user should have no expectation of privacy. Unauthorized access or violations of security regulations is unlawful and hence if monitoring reveals either of it, appropriate disciplinary action will be taken against the users violating security regulations or making unauthorized use of this system."
echo "********************************************************************************"
EOF'
    sudo chmod +x /etc/update-motd.d/99-company-banner
    if [ $? -eq 0 ]; then
        log "Logon banner set."
    else
        log "Failed to set logon banner."
    fi
    sleep 15
}

# Function to set autologout
set_autologout() {
    log "Setting autologout..."
    echo "export TMOUT=300" | sudo tee -a /etc/profile
    echo "readonly TMOUT" | sudo tee -a /etc/profile
    if [ $? -eq 0 ]; then
        log "Autologout settings added."
    else
        log "Failed to set autologout."
    fi
    sleep 15
}

# Function to set history format
set_history_format() {
    log "Setting history format..."
    echo '# Set the timestamp format for command history' | sudo tee -a /etc/bash.bashrc
    echo 'export HISTTIMEFORMAT="%F %T "' | sudo tee -a /etc/bash.bashrc
    source /etc/bash.bashrc
    if [ $? -eq 0 ]; then
        log "History format set."
    else
        log "Failed to set history format."
    fi
    sleep 15
}

# Function to install Sophos and UEMS agent
install_sophos_uems() {
    log "Installing Sophos..."
    bash /home/ubuntu/SophosSetup.sh
    if [ $? -eq 0 ]; then
        log "Sophos installed."
    else
        log "Failed to install Sophos."
    fi
    sleep 15

    log "Installing UEMS agent..."
    cd /home/ubuntu/AWSServers_UEMSLinuxAgent/ || { log "Failed to change directory to UEMS agent."; return; }
    sudo sh install.sh
    if [ $? -eq 0 ]; then
        log "UEMS agent installed."
    else
        log "Failed to install UEMS agent."
    fi
    sleep 15
}

# Function to update, upgrade, and reboot (commented out updates for now)
update_upgrade_reboot() {
    # log "Updating and upgrading system..."
    # sudo apt-get update && sudo apt-get upgrade -y
    # if [ $? -eq 0 ]; then
    #     log "System updated and upgraded."
    # else
    #     log "Failed to update/upgrade system."
    # fi
    sleep 15
    read -p "Do you want to reboot the server now? (y/n): " confirm
    if [[ "$confirm" == "y" ]]; then
        sudo reboot
        log "Server reboot initiated."
    else
        log "Reboot skipped."
    fi
}

# Main script execution
log "Starting SOE script."
install_ntp
set_hostname
set_logon_banner
set_autologout
set_history_format
install_sophos_uems
update_upgrade_reboot

# Add NTP configuration at the end
log "Appending NTP server entries to configuration file."
echo "server samay1.nic.in iburst" | sudo tee -a /etc/ntpsec/ntp.conf
echo "server samay2.nic.in iburst" | sudo tee -a /etc/ntpsec/ntp.conf
log "NTP servers added."
sudo systemctl restart ntp
log "NTP service restarted."

log "SOE steps completed."
```

## Usage

1. Make the Script Executable
```sh
chmod +x soe.sh
```

2. Run the Script
```sh
sudo bash ./soe.sh
```

> Note: Ensure you have sudo privileges to execute the script.

3. Logging:
   
The script maintains a log file at:
```sh
/var/log/soe_script.log
```

You can check the log file for the execution status of each step:

```sh
cat /var/log/soe_script.log
```

### Customization
- Modify hostname during script execution.

- Edit /etc/update-motd.d/99-company-banner for custom logon banners.

- Adjust timeout settings in /etc/profile.

- NTP Configuration
After all installations, the script appends the following servers to /etc/ntpsec/ntp.conf:
`server samay1.nic.in iburst`
`server samay2.nic.in iburst`
You can update these server addresses as needed.

### Known Issues
**APT is locked:** Some processes may be using APT, causing installation failures. Ensure no package manager processes (apt, dpkg) are running before executing the script.

**Manual Reboot:** The script prompts the user for reboot confirmation. If skipped, reboot manually using:

```sh
sudo reboot
```
