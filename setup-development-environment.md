# Setting up a Development Environment on Ubuntu

This document provides a step-by-step guide to setting up a development environment on an Ubuntu system. It covers the installation and configuration of various tools and utilities commonly used for software development.

### Prerequisites

1. An Ubuntu system with internet access
2. `sudo` privileges to install packages and modify system files

### 1. Update Package Lists and Install Basic Utilities
```bash
sudo apt-get update
sudo apt-get install ca-certificates curl unzip vim -y
```
This command updates the package lists and installs essential utilities like `ca-certificates` (SSL/TLS certificates), `curl` (for transferring data), `unzip` (for extracting compressed files), and `vim` (a text editor).

### 2. Install Docker
```bash
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
```
These commands set up the Docker repository and install the Docker Engine, CLI, containerd.io runtime, and additional plugins like BuildX and Docker Compose.

**Docker Post-Installation Checks**
```bash
docker images -a  # List all Docker images
docker pull hello-world  # Pull the "hello-world" image
docker run hello-world  # Run the "hello-world" container
docker rmi hello-world  # Remove the "hello-world" image
```
These commands list all Docker images, pull the "hello-world" image, run the "hello-world" container, and then remove the "hello-world" image.

### 3. Add Current User to the Docker Group
```bash
sudo usermod -aG docker $USER
```
This command adds the current user to the docker group, allowing them to run Docker commands without sudo.

### 4. Create and Configure SSH Key Pair for GitHub
```bash
ssh-keygen -o -t rsa -C "ssh@github.com"
cat ~/.ssh/id_rsa.pub  # Copy the public key output
```
These commands create a new SSH key pair for authenticating with GitHub. The public key output should be added to your GitHub account settings.

### 5. Create a Directory for Code Repositories
```bash
mkdir code
cd code
```
This creates a new directory called "code" and changes the current working directory to it.

### 6. Clone GitHub Repositories
```bash
git clone git@github.com:YOUR_USERNAME/REPO_NAME.git
cd REPO_NAME
git status  # Check the repository status
git fetch --all  # Fetch all branches and tags
git checkout BRANCH_OR_TAG_NAME  # Switch to a specific branch or tag
```
Replace `YOUR_USERNAME`, `REPO_NAME`, and `BRANCH_OR_TAG_NAME` with your actual values. These commands clone a GitHub repository, check its status, fetch all branches and tags, and switch to a specific branch or tag.

### 7. Install Java Development Kit (JDK)
```bash
sudo apt-get install openjdk-18-jdk -y
```
This command installs the OpenJDK 18 Java Development Kit.

### 8. Install Node.js and npm
```bash
sudo apt-get install nodejs -y
```
This command installs the Node.js JavaScript runtime and the Node Package Manager (npm).

### 9. Install Apache Maven
```bash
sudo apt-get install maven -y
```
This command installs Apache Maven, a build automation tool for Java projects.

### 10. Install Python 3 and pip
```bash
sudo apt-get install python3 python3-pip -y
```
This command installs Python 3 and the Python package installer (pip).

### 11. Install AWS CLI
```bash
sudo apt-get install awscli -y
```
This command installs the AWS Command Line Interface (CLI) using the system package manager.

### 12. Install AWS Copilot
```bash
curl -Lo copilot https://github.com/aws/copilot-cli/releases/latest/download/copilot-linux && chmod +x copilot && sudo mv copilot /usr/local/bin/copilot
copilot --version  # Check the installed version
```
These commands download and install the AWS Copilot CLI, and then check the installed version.

### 13. Authenticate with AWS ECR
```bash
aws ecr get-login-password --region YOUR_AWS_REGION | docker login --username AWS --password-stdin YOUR_ECR_URI
```
Replace `YOUR_AWS_REGION` and `YOUR_ECR_URI` with your actual AWS region and ECR URI. This command retrieves an authentication token for the AWS ECR and logs in to the ECR registry using the Docker client.

### 14. Deploy Services with AWS Copilot
```bash
cd YOUR_SERVICE_DIRECTORY
copilot svc deploy --env prod
```
Replace `YOUR_SERVICE_DIRECTORY` with the directory containing your service code. This command deploys the service using AWS Copilot.

### 15. Install Terraform
```bash
sudo apt-get update
sudo apt-get install -y gnupg software-properties-common
wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update
sudo apt-get install terraform -y
```
These commands add the HashiCorp repository and install Terraform, an infrastructure as code (IaC) tool for provisioning and managing cloud resources.

### 16. Install Gradle
```bash
wget -c https://services.gradle.org/distributions/gradle-7.4.2-bin.zip -P /tmp
sudo unzip -d /opt/gradle /tmp/gradle-7.4.2-bin.zip
sudo tee /etc/profile.d/gradle.sh > /dev/null << 'EOF'
export GRADLE_HOME=/opt/gradle/gradle-7.4.2
export PATH=$PATH:$GRADLE_HOME/bin
EOF
source /etc/profile.d/gradle.sh
```
These commands download and install Gradle 7.4.2, a build automation tool for Java, and set up the necessary environment variables.

After completing these steps, you should have a development environment set up on your Ubuntu system with various tools and utilities installed, including Docker, Java, Node.js, Maven, Python, AWS CLI, AWS Copilot, Terraform, and Gradle. Additionally, you've created an SSH key pair for GitHub, cloned repositories, and learned how to authenticate with AWS ECR and deploy services using AWS Copilot.

> **Note:** Depending on your project requirements, you may need to install additional packages or configure specific tools further. This guide provides a basic setup for common development tasks.
