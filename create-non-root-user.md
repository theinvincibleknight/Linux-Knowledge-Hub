Step-by-step guide on creating a key pair, creating a new non-root user, assigning the key pair to that user, and securing SSH access on an AWS instance:

## Create a Key Pair in AWS:

1. Log in to the AWS Management Console.
2. Go to the EC2 Dashboard.
3. In the sidebar, click on "Key Pairs" under the "Network & Security" section.
4. Click on "Create Key Pair."
5. Provide a name for the key pair and select "Create." This will download the private key (.pem file) to your local machine. Ensure to keep this key secure.

## Retrieve Public Key from the Key Pair:

1. Open `PowerShell` as **Administrator**.
2. Run the following command to retrieve the public key from the private key (.pem file):

```powershell
ssh-keygen -y -f "jon.smith.pem"
```

This generates the public key. Alternatively, you can use **Puttygen** to extract the public key.

## Create a New Non-Root User and Assign Keypair:

1. Connect to your AWS instance using SSH as the root user.
2. Create a new user:

```bash
sudo adduser jonsmith
```
3. Set an empty password for the user:

```bash
sudo passwd -d jonsmith
```
4. Set password expiry date to Never:

```bash
sudo chage -I -1 -m 0 -M 99999 -E -1 jonsmith
```
5. Switch to the new user:

```bash
su - jonsmith
```
6. Create the necessary directory and file:

```bash
mkdir ~/.ssh
vi ~/.ssh/authorized_keys
```
7. Open the `authorized_keys` file and paste the retrieved public key in a single line.
8. Set appropriate permissions:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```
## Check and Disable Password Authentication:

1. Open the SSH configuration file:
```bash
sudo vi /etc/ssh/sshd_config
```
2. Ensure the following settings:
```yaml
PubkeyAuthentication yes
PasswordAuthentication no
```
Save the file and exit the editor.

3. Restart the SSH service:

```bash
sudo service ssh restart
```
These steps will create a new key pair, a new non-root user, assign the key pair to that user, and secure SSH access on your AWS instance by disabling password authentication and enabling public key authentication.
