# Launch Your First Amazon EC2 Instance

This guide walks you through deploying your first virtual machine on AWS using Amazon EC2.

## Prerequisites

- An AWS account
- Basic understanding of cloud computing concepts
- SSH client installed on your local machine (for Windows, use PuTTY)

## Steps

### 1. Launch an EC2 Instance from AWS Management Console

1. **Sign in to AWS Console**
   - Go to [AWS Console](https://aws.amazon.com/) and click "Sign In to the Console"
   - Enter your credentials

2. **Navigate to EC2 Service**
   - Search for "EC2" in the services search bar
   - Click on "EC2" under Services

3. **Launch an Instance**
   - Click the "Launch Instance" button

### 2. Use Amazon Linux 2 AMI

4. **Choose an AMI**
   - Name your instance (e.g., "MyFirstEC2")
   - Select "Amazon Linux" (free tier eligible)
   - Keep default selection

5. **Choose Instance Type**
   - Select "t2.micro" (free tier eligible)
   - Click "Next: Configure Instance Details"

6. **Configure Instance**
   - Leave defaults for first instance
   - Click through storage and tags sections

### 3. Configure Security Groups for SSH Access

7. **Set Up Security Group**
   - Create new security group (e.g., "ssh-access")
   - Add rule:
     - Type: SSH
     - Protocol: TCP
     - Port: 22
     - Source: For testing use "Anywhere (0.0.0.0/0)"
   - Click "Review and Launch"

8. **Launch Instance**
   - Review settings
   - Click "Launch"

9. **Create Key Pair**
   - Select "Create new key pair"
   - Name it (e.g., "my-first-keypair")
   - Download the .pem file
   - **Important:** Store this securely
   - Click "Launch Instance"

### 4. Connect to Instance via SSH

10. **Find Connection Details**
    - In EC2 Dashboard, select your instance
    - Note the "Public IPv4 address" or "Public IPv4 DNS"

11. **Connect Using SSH**

    #### Linux/Mac
    ```bash
    chmod 400 your-key-pair.pem
    ssh -i "your-key-pair.pem" ec2-user@your-instance-public-dns
    ```

    #### Windows (Using PuTTY)
    1. Convert .pem to .ppk using PuTTYgen
    2. Open PuTTY
    3. Enter: `ec2-user@your-instance-public-dns`
    4. Under Connection > SSH > Auth, browse to select .ppk file
    5. Click Open

## Clean Up Resources

To avoid unnecessary charges:

```bash
1. Go to EC2 Dashboard
2. Select your instance
3. Click "Instance State" > "Terminate Instance"
4. Confirm termination
