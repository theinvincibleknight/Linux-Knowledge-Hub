# Add or remove a public key on your instance

In AWS, if we specify a key pair at launch your, when instance boots for the first time, the public key material is placed on your Linux instance in an entry within `~/.ssh/authorized_keys`.

You can change the key pair that is used to access the default system account of your instance by adding a new public key on the instance, or by replacing the public key (deleting the existing public key and adding a new one) on the instance. You can also remove all public keys from an instance. You might take these actions for the following reasons:

- If a user in your organization requires access to the system user using a separate key pair, you can add the public key to your instance.

- If someone has a copy of the private key (`.pem` file) and you want to prevent them from connecting to your instance (for example, if they've left your organization), you can delete the public key on the instance and replace it with a new one.

- If you create a Linux AMI from an instance, the public key material is copied from the instance to the AMI. If you launch an instance from the AMI, the new instance includes the public key from the original instance. To prevent someone who has the private key from connecting to the new instance, you can remove the public key from the original instance before creating the AMI.

The public keys are located in the .ssh/authorized_keys file on the instance.

To add or replace a key pair, you must be able to connect to your instance. If you've lost your existing private key or you launched your instance without a key pair, you won't be able connect to your instance and therefore won't be able to add or replace a key pair.

## To add or replace a key pair

1. Create a new key pair using the Amazon EC2 console or a third-party tool.
    - Save the **Private Key** on local machine.

2. Retrieve the public key from your new key pair.
    - In below example, we are using PowerShell to retrieve the **Public Key** from your saved **Private Key**.
    - Navigate to path where you have saved private key using `cd` command.
    - Run below command in `PowerShell`.

```PowerShell
ssh-keygen -y -f "Private_Key_New.pem"
```
You should be able to see **public key information** output in below format:

```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCtsli/X0FY04ktJ+LTjedW10LqXqbMim3c1rbRX3rjifjTpQe1BRT7v9O2YKYQf/CT9ynSBRnTObP16UjYCEeg/L0R0XPr23utxeokMFfT1NQlADJ1YE6ao6u0Wzx51V6i8hAiDuDuRrP8MuwxVaiC/sUVsdbNKrBqJerhWiQEvgscVpFBbkQRURapjB9zgGcdFT/+rk5k+92q8KFDoM+PcrCH+m916j5/4GQQcs/kVFaZFioajF4CW3yvBcPSsjF5kfuEyQxIWP5hH4Apqel3hcr4koSZBT/Reea4gm4RzZLfkEOCIcjor5wXvKBZtv9GqNEjsRBOCHY0ssafCMTJ
```

3. Connect to your instance using your existing private key.

```PowerShell
ssh -i "Private_Key_Old.pem" ubuntu@ec2-13-128-296-234.ap-south-1.compute.amazonaws.com
```

Or you can connect your instance using `Putty` or using `Connect` option from **AWS Console**.

4. Once connected, navigate to directory where the pubic key is saved in the instance using `cd` command.

```bash
cd /home/ubuntu/.ssh/
```
- The public keys are located in the `.ssh/authorized_keys` file on the instance. Using a text editor of your choice, open the `authorized_keys` file on the instance. Paste the public key information (Step 2 Output) from your new key pair underneath the existing public key information. Save the file.

5. Disconnect from your instance, and test that you can connect to your instance using the new private key file.

    You should be able to connect the instance using both the Original/Old and New Private Keys, as `.ssh/authorized_keys` holds public key information of both the keys.

6. (Optional) If you're replacing an existing key pair, connect to your instance and delete the public key information for the original key pair from the .ssh/authorized_keys file.