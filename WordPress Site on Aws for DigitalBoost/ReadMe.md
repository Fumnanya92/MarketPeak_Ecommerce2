
# Nonso Please find attached.

# WordPress Site on AWS

# 1. Create a VPC

# Guide to Create a Virtual Private Cloud (VPC) for WordPress Infrastructure

## Step 1: Define Address Range for the VPC

1. Determine the CIDR block for the VPC.
   - Example: `10.0.0.0/16`

## Step 2: Create VPC with Public and Private Subnets

1. **Create the VPC:**
   - Navigate to the VPC Dashboard in your AWS Management Console.
   - Click on "Create VPC".
   - Enter the VPC name (e.g., `WordPress-VPC`).
   - Set the IPv4 CIDR block to `10.0.0.0/16`.
   - Select "Default" for Tenancy.
   - Click on "Create VPC".

2. **Create Subnets:**
   - Go to the Subnets section in the VPC Dashboard.
   - Click on "Create Subnet".

   **Public Subnet:**
   - Name: `Public-Subnet`
   - VPC: `WordPress-VPC`
   - Availability Zone: Choose one (e.g., `us-west-2a`)
   - IPv4 CIDR block: `10.0.1.0/24`
   - Click on "Create subnet".

   **Private Subnet:**
   - Name: `Private-Subnet`
   - VPC: `WordPress-VPC`
   - Availability Zone: Choose one (e.g., `us-west-2a`)
   - IPv4 CIDR block: `10.0.2.0/24`
   - Click on "Create subnet".

## Step 3: Configure Route Tables for Each Subnet

1. **Create Route Table for Public Subnet:**
   - Go to the Route Tables section in the VPC Dashboard.
   - Click on "Create Route Table".
   - Name: `Public-Route-Table`
   - VPC: `WordPress-VPC`
   - Click on "Create route table".

   **Add Internet Gateway:**
   - Go to the Internet Gateways section.
   - Click on "Create Internet Gateway".
   - Name: `WordPress-IGW`
   - Click on "Create internet gateway".
   - Select the newly created Internet Gateway and click "Attach to VPC".
   - Choose `WordPress-VPC` and click "Attach".

   **Update Route Table:**
   - Go back to the Route Tables section.
   - Select `Public-Route-Table`.
   - Go to the Routes tab and click on "Edit routes".
   - Click on "Add route".
   - Destination: `0.0.0.0/0`
   - Target: Select `Internet Gateway` and choose `WordPress-IGW`.
   - Click "Save routes".

   **Associate Public Subnet:**
   - Go to the Subnet Associations tab.
   - Click on "Edit subnet associations".
   - Select `Public-Subnet`.
   - Click "Save associations".

2. **Create Route Table for Private Subnet:**
   - Go to the Route Tables section in the VPC Dashboard.
   - Click on "Create Route Table".
   - Name: `Private-Route-Table`
   - VPC: `WordPress-VPC`
   - Click on "Create route table".

   **Update Route Table:**
   - This route table will not have a route to the Internet Gateway, so it uses the default local route.

   **Associate Private Subnet:**
   - Go to the Subnet Associations tab.
   - Click on "Edit subnet associations".
   - Select `Private-Subnet`.
   - Click "Save associations".

## Summary
- **VPC**: `WordPress-VPC` (`10.0.0.0/16`)
- **Subnets**:
  - Public Subnet: `10.0.1.0/24`
  - Private Subnet: `10.0.2.0/24`
- **Route Tables**:
  - Public Route Table with route to Internet Gateway
  - Private Route Table with local route only
- **Internet Gateway**: `WordPress-IGW` attached to the VPC

# Step-by-Step Guide to Implement a Secure Network Architecture with Public and Private Subnets

## Step 1: Set Up Public Subnet for Resources Accessible from the Internet

1. **Create the Public Subnet:**
   - Navigate to the VPC Dashboard in your AWS Management Console.
   - Go to the Subnets section.
   - Click on "Create Subnet".
   - Name: `Public-Subnet`
   - VPC: `Your-VPC`
   - Availability Zone: Choose one (e.g., `us-west-2a`)
   - IPv4 CIDR block: `10.0.1.0/24`
   - Click on "Create subnet".

2. **Create and Attach an Internet Gateway:**
   - Go to the Internet Gateways section.
   - Click on "Create Internet Gateway".
   - Name: `My-Internet-Gateway`
   - Click on "Create internet gateway".
   - Select the newly created Internet Gateway and click "Actions" > "Attach to VPC".
   - Choose `Your-VPC` and click "Attach".

3. **Create and Configure a Route Table for the Public Subnet:**
   - Go to the Route Tables section.
   - Click on "Create Route Table".
   - Name: `Public-Route-Table`
   - VPC: `Your-VPC`
   - Click on "Create route table".
   - Select `Public-Route-Table`.
   - Go to the Routes tab and click on "Edit routes".
   - Click on "Add route".
   - Destination: `0.0.0.0/0`
   - Target: Select `Internet Gateway` and choose `My-Internet-Gateway`.
   - Click "Save routes".
   - Go to the Subnet Associations tab.
   - Click on "Edit subnet associations".
   - Select `Public-Subnet`.
   - Click "Save associations".

## Step 2: Create Private Subnet for Resources with No Direct Internet Access

1. **Create the Private Subnet:**
   - Go to the Subnets section.
   - Click on "Create Subnet".
   - Name: `Private-Subnet`
   - VPC: `Your-VPC`
   - Availability Zone: Choose one (e.g., `us-west-2a`)
   - IPv4 CIDR block: `10.0.2.0/24`
   - Click on "Create subnet".

2. **Create and Configure a Route Table for the Private Subnet:**
   - Go to the Route Tables section.
   - Click on "Create Route Table".
   - Name: `Private-Route-Table`
   - VPC: `Your-VPC`
   - Click on "Create route table".
   - Select `Private-Route-Table`.
   - Go to the Routes tab and click on "Edit routes".
   - Add the default local route automatically provided (`10.0.0.0/16` -> `local`).
   - Go to the Subnet Associations tab.
   - Click on "Edit subnet associations".
   - Select `Private-Subnet`.
   - Click "Save associations".

## Step 3: Configure a NAT Gateway for Private Subnet Internet Access

1. **Create an Elastic IP for the NAT Gateway:**
   - Navigate to the Elastic IPs section in the VPC Dashboard.
   - Click on "Allocate Elastic IP address".
   - Click "Allocate".
   - Note the allocated Elastic IP.

2. **Create the NAT Gateway:**
   - Go to the NAT Gateways section.
   - Click on "Create NAT Gateway".
   - Subnet: Select `Public-Subnet`.
   - Elastic IP Allocation ID: Select the Elastic IP you just created.
   - Click on "Create NAT Gateway".

3. **Update the Route Table for the Private Subnet:**
   - Go to the Route Tables section.
   - Select `Private-Route-Table`.
   - Go to the Routes tab and click on "Edit routes".
   - Click on "Add route".
   - Destination: `0.0.0.0/0`
   - Target: Select `NAT Gateway` and choose the newly created NAT Gateway.
   - Click "Save routes".

## Summary
- **VPC**: `Your-VPC`
- **Subnets**:
  - Public Subnet: `10.0.1.0/24`
  - Private Subnet: `10.0.2.0/24`
- **Route Tables**:
  - Public Route Table with route to Internet Gateway
  - Private Route Table with route to NAT Gateway
- **Internet Gateway**: `My-Internet-Gateway` attached to the VPC
- **NAT Gateway**: Created in the Public Subnet with an associated Elastic IP

  # Step-by-Step Guide to Deploy a Managed MySQL Database Using Amazon RDS for WordPress Data Storage

# create an instance 
# Step-by-Step Guide to Launch an EC2 Instance

## Step 1: Navigate to the EC2 Dashboard

1. **Log in to your AWS Management Console:**
   - Open your web browser and go to the AWS Management Console.
   - Sign in with your AWS account credentials.

2. **Access EC2 Dashboard:**
   - In the AWS Management Console, search for `EC2` in the services search bar.
   - Click on `EC2` to open the EC2 Dashboard.

## Step 2: Launch an Instance

1. **Click "Launch Instance":**
   - On the EC2 Dashboard, click the `Launch instance` button.

2. **Choose an Amazon Machine Image (AMI):**
   - Select an AMI from the list. You can choose from Amazon Linux, Ubuntu, Windows, or other pre-configured images.
   - Click `Select` next to the desired AMI.

3. **Choose an Instance Type:**
   - Select the instance type that suits your needs (e.g., `t2.micro` for free tier eligible).
   - Click `Next: Configure Instance Details`.

4. **Configure Instance Details:**
   - Configure the instance details as needed. For a basic setup, you can leave the default settings.
   - Key configurations include:
     - Number of instances: Typically `1`.
     - Network: Select your VPC.
     - Subnet: Choose a subnet within your VPC.
     - Auto-assign Public IP: Enable if you need direct internet access.
   - Click `Next: Add Storage`.

5. **Add Storage:**
   - Configure the storage for your instance. The default storage settings should be sufficient for basic use.
   - Click `Next: Add Tags`.

6. **Add Tags:**
   - Add tags to help identify and manage your instance. Example:
     - Key: `Name`
     - Value: `MyEC2Instance`
   - Click `Next: Configure Security Group`.

7. **Configure Security Group:**
   - Create a new security group or select an existing one.
   - Add rules to allow necessary traffic (e.g., SSH for Linux or RDP for Windows).
     - Example for SSH access:
       - Type: `SSH`
       - Protocol: `TCP`
       - Port Range: `22`
       - Source: `0.0.0.0/0` (For public access, restrict IP range for security)
   - Click `Review and Launch`.

8. **Review and Launch:**
   - Review your instance configuration.
   - Click `Launch`.

9. **Select a Key Pair:**
   - Select an existing key pair or create a new key pair.
   - If creating a new key pair, download the key pair file (`.pem`) and keep it secure. You will need it to access your instance.
   - Check the acknowledgment box.
   - Click `Launch Instances`.

10. **View Instances:**
    - Click `View Instances` to see your newly launched instance.
    - Wait for the instance state to change to `running`.

## Step 3: Connect to Your Instance

1. **Get the Public DNS Name or IP Address:**
   - In the EC2 Dashboard, select your instance.
   - Copy the Public DNS name or IP address.

2. **Connect via SSH (for Linux) or RDP (for Windows):**
   - **For Linux:**
     - Open a terminal.
     - Run the following command to connect (replace `your-key-pair.pem` and `your-public-dns` with your details):
       ```bash
       ssh -i /path/to/your-key-pair.pem ec2-user@your-public-dns
       ```
   - **For Windows:**
     - Use Remote Desktop Connection.
     - Enter the Public DNS or IP address of your instance.
     - Log in with the username `Administrator` and the password retrieved from the EC2 console.

## Summary

You have successfully launched an EC2 instance on AWS and connected to it. Your instance is now ready for use, and you can configure it further as needed.

- **Instance Type**: Based on your selection (e.g., `t2.micro`)
- **AMI**: Based on your selection (e.g., Amazon Linux, Ubuntu)
- **Security Group**: Configured to allow necessary access
- **Key Pair**: Used to securely connect to your instance

This setup ensures that your EC2 instance is launched and accessible for further configuration and use.
![image](https://github.com/Fumnanya92/Darey.io_Projects/assets/104866089/aaef8aa8-3559-4a24-8b5b-16f4cf8ff27e)

# Step-by-Step Guide to SSH into an EC2 Instance and Run Commands

## Step 1: SSH into the EC2 Instance

1. **Open Terminal (Linux/Mac) or Command Prompt/PowerShell (Windows):**

   - On your local machine, open the terminal or command prompt.

2. **Navigate to the Directory with Your Key Pair File:**

   - Change to the directory where your `.pem` key pair file is located. Example:
     ```bash
     cd /path/to/your-key-pair.pem
     ```

3. **SSH into the Instance:**

   - Use the following command to SSH into your instance. Replace `/path/to/your-key-pair.pem` with the path to your `.pem` file and `your-public-dns` with the Public DNS name or IP address of your EC2 instance:
     ```bash
     ssh -i /path/to/your-key-pair.pem ec2-user@your-public-dns
     ```

   - Example:
     ```bash
     ssh -i /Users/username/keys/my-key-pair.pem ec2-user@ec2-54-123-45-67.compute-1.amazonaws.com
     ```

   - If this is your first time connecting to this instance, you may see a warning about the authenticity of the host. Type `yes` and press Enter to continue.

## Step 2: Run `sudo su` and `yum update -y`

1. **Switch to the Root User:**

   - Once connected, run the following command to switch to the root user:
     ```bash
     sudo su
     ```

2. **Update All Installed Packages:**

   - Run the following command to update all installed packages:
     ```bash
     yum update -y
     ```

3. **Wait for the Update to Complete:**

   - The `yum update -y` command will download and install the latest updates for all installed packages. This process may take a few minutes.

## Summary

You have successfully SSH'd into your EC2 instance, switched to the root user, and updated all installed packages using `yum`. 

- **SSH Command**: `ssh -i /path/to/your-key-pair.pem ec2-user@your-public-dns`
- **Switch to Root User**: `sudo su`
- **Update Packages**: `yum update -y`

This setup ensures that your EC2 instance is up-to-date with the latest package updates, which can include important security and performance improvements.



## Step 1: Create an Amazon RDS Instance with MySQL Engine

1. **Navigate to RDS Dashboard:**
   - Log in to your AWS Management Console.
   - Go to the RDS Dashboard.

2. **Create a New Database:**
   - Click on "Create database".
   - Select the "Standard Create" option.
   - Engine options: Choose `MySQL`.
   - Version: Select the desired MySQL version (e.g., `MySQL 8.0`).

3. **Specify DB Details:**
   - DB instance identifier: `wordpress-db`.
   - Master username: Choose a username (e.g., `admin`).
   - Master password: Set a secure password and confirm it.

4. **Configure Instance:**
   - DB instance class: Choose an instance type (e.g., `db.t3.micro` for testing or `db.t3.medium` for production).
   - Storage type: Select `General Purpose (SSD)` and specify the allocated storage (e.g., `20 GB`).

5. **Set Up Networking:**
   - VPC: Choose the VPC where your WordPress application is running.
   - Subnet group: Select a subnet group that includes subnets in different Availability Zones.
   - Public access: Set to `No` for enhanced security.
   - VPC security groups: Choose or create a security group that allows access from your WordPress instances.
   - Availability Zone: Choose `No preference` for high availability.

6. **Database Options:**
   - Initial database name: `wordpress_db`.
   - Leave other settings as default.

7. **Create Database:**
   - Review your settings and click "Create database".

## Step 2: Configure Security Groups for RDS Instance

1. **Create a Security Group for RDS:**
   - Navigate to the VPC Dashboard.
   - Click on "Security Groups".
   - Click "Create security group".
   - Name: `wordpress-rds-sg`.
   - Description: `Security group for WordPress RDS instance`.
   - VPC: Select the VPC where your RDS instance is running.
   - Click "Create".

2. **Add Inbound Rules:**
   - Select `wordpress-rds-sg`.
   - Go to the "Inbound rules" tab and click "Edit inbound rules".
   - Click "Add rule".
   - Type: `MySQL/Aurora`.
   - Protocol: `TCP`.
   - Port range: `3306`.
   - Source: Select `Custom` and specify the security group used by your WordPress instances (e.g., `wordpress-sg`).
   - Click "Save rules".

3. **Associate Security Group with RDS Instance:**
   - Go to the RDS Dashboard.
   - Select your RDS instance (`wordpress-db`).
   - Click on the "Modify" button.
   - In the "Connectivity" section, under "VPC security groups", select `wordpress-rds-sg`.
   - Click "Continue" and then "Apply immediately".

# Step-by-Step Guide to Install MySQL 5.7 and Connect to an RDS Database

## Step 1: SSH into the EC2 Instance

1. **Open Terminal (Linux/Mac) or Command Prompt/PowerShell (Windows):**

   - On your local machine, open the terminal or command prompt.

2. **Navigate to the Directory with Your Key Pair File:**

   - Change to the directory where your `.pem` key pair file is located. Example:
     ```bash
     cd /path/to/your-key-pair.pem
     ```

3. **SSH into the Instance:**

   - Use the following command to SSH into your instance. Replace `/path/to/your-key-pair.pem` with the path to your `.pem` file and `your-public-dns` with the Public DNS name or IP address of your EC2 instance:
     ```bash
     ssh -i /path/to/your-key-pair.pem ec2-user@your-public-dns
     ```

   - Example:
     ```bash
     ssh -i /Users/username/keys/my-key-pair.pem ec2-user@ec2-54-123-45-67.compute-1.amazonaws.com
     ```

   - If this is your first time connecting to this instance, you may see a warning about the authenticity of the host. Type `yes` and press Enter to continue.

## Step 2: Run `sudo su` and `yum update -y`

1. **Switch to the Root User:**

   - Once connected, run the following command to switch to the root user:
     ```bash
     sudo su
     ```

2. **Update All Installed Packages:**

   - Run the following command to update all installed packages:
     ```bash
     yum update -y
     ```

## Step 3: Install MySQL 5.7

1. **Download and Install the MySQL 5.7 Repository:**

   - Run the following command to download and install the MySQL 5.7 repository:
     ```bash
     sudo rpm -Uvh https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
     ```

2. **Import the MySQL GPG Key:**

   - Run the following command to import the MySQL GPG key:
     ```bash
     sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2022
     ```

3. **Install MySQL Community Server:**

   - Run the following command to install MySQL community server:
     ```bash
     sudo yum install mysql-community-server -y
     ```

4. **Install MySQL Server:**

   - Run the following command to ensure MySQL server is installed:
     ```bash
     sudo yum install mysql-server -y
     ```

5. **Enable MySQL to Start on Boot:**

   - Run the following command to enable MySQL to start on boot:
     ```bash
     sudo systemctl enable mysqld
     ```

6. **Start the MySQL Service:**

   - Run the following command to start the MySQL service:
     ```bash
     sudo systemctl start mysqld
     ```

## Step 4: Connect to the RDS MySQL Database

1. **Connect to the RDS MySQL Database:**

   - Run the following command to connect to your RDS MySQL database. Replace `databaseforwordpress.cdyq80q08jtz.us-east-1.rds.amazonaws.com`, `admin`, and `password` with your RDS endpoint, username, and password:
     ```bash
     mysql -h databaseforwordpress.cdyq80q08jtz.us-east-1.rds.amazonaws.com -u admin -p
     ```

   - When prompted, enter the password for the `admin` user.

## Summary

You have successfully SSH'd into your EC2 instance, installed MySQL 5.7, and connected to your RDS MySQL database.

- **SSH Command**: `ssh -i /path/to/your-key-pair.pem ec2-user@your-public-dns`
- **Switch to Root User**: `sudo su`
- **Update Packages**: `yum update -y`
- **Install MySQL 5.7**:
  - `sudo rpm -Uvh https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm`
  - `sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2022`
  - `sudo yum install mysql-community-server -y`
  - `sudo yum install mysql-server -y`
- **Enable and Start MySQL**:
  - `sudo systemctl enable mysqld`
  - `sudo systemctl start mysqld`
- **Connect to RDS**:
  - `mysql -h databaseforwordpress.cdyq80q08jtz.us-east-1.rds.amazonaws.com -u admin -p`

This setup ensures that your EC2 instance has MySQL 5.7 installed and you can successfully connect to your RDS MySQL database.



## Step 3: Connect WordPress to the RDS Database

1. **Retrieve Database Endpoint:**
   - Go to the RDS Dashboard.
   - Select your RDS instance (`wordpress-db`).
   - Copy the "Endpoint" (e.g., `wordpress-db.xxxxxxx.us-west-2.rds.amazonaws.com`).

2. **Update WordPress Configuration:**
   - SSH into your WordPress server.
   - Open the `wp-config.php` file located in your WordPress directory.
   - Update the database configuration settings as follows:

   ```php
   /** The name of the database for WordPress */
   define('DB_NAME', 'wordpress_db');

   /** MySQL database username */
   define('DB_USER', 'admin');

   /** MySQL database password */
   define('DB_PASSWORD', 'your_password');

   /** MySQL hostname */
   define('DB_HOST', 'wordpress-db.xxxxxxx.us-west-2.rds.amazonaws.com');

   /** Database Charset to use in creating database tables. */
   define('DB_CHARSET', 'utf8mb4');

   /** The Database Collate type. Don't change this if in doubt. */
   define('DB_COLLATE', '');


   # Step-by-Step Guide to Utilize Amazon Elastic File System (EFS) for WordPress Files

## Step 1: Create an EFS File System

1. **Navigate to EFS Dashboard:**
   - Log in to your AWS Management Console.
   - Go to the EFS Dashboard.

2. **Create a New File System:**
   - Click on "Create file system".
   - Name the file system (e.g., `wordpress-efs`).

3. **Configure File System Settings:**
   - VPC: Choose the VPC where your WordPress instances are running.
   - Availability and Durability: Select "Regional" for multi-AZ access.
   - Performance mode: Choose "General Purpose".
   - Throughput mode: Choose "Bursting" for typical use cases.
   - Encryption: Enable encryption if required.

4. **Configure Access Points:**
   - Click "Next".
   - Click "Add access point" if you need specific access point configurations, or proceed with the default settings.
   
5. **Review and Create:**
   - Review all the settings.
   - Click "Create".
  
# Step-by-Step Guide to Install Apache HTTP Server on an EC2 Instance

## Step 1: SSH into the EC2 Instance

1. **Open Terminal (Linux/Mac) or Command Prompt/PowerShell (Windows):**

   - On your local machine, open the terminal or command prompt.

2. **Navigate to the Directory with Your Key Pair File:**

   - Change to the directory where your `.pem` key pair file is located. Example:
     ```bash
     cd /path/to/your-key-pair.pem
     ```

3. **SSH into the Instance:**

   - Use the following command to SSH into your instance. Replace `/path/to/your-key-pair.pem` with the path to your `.pem` file and `your-public-dns` with the Public DNS name or IP address of your EC2 instance:
     ```bash
     ssh -i /path/to/your-key-pair.pem ec2-user@your-public-dns
     ```

   - Example:
     ```bash
     ssh -i /Users/username/keys/my-key-pair.pem ec2-user@ec2-54-123-45-67.compute-1.amazonaws.com
     ```

   - If this is your first time connecting to this instance, you may see a warning about the authenticity of the host. Type `yes` and press Enter to continue.

## Step 2: Run `sudo su` and `yum update -y`

1. **Switch to the Root User:**

   - Once connected, run the following command to switch to the root user:
     ```bash
     sudo su
     ```

2. **Update All Installed Packages:**

   - Run the following command to update all installed packages:
     ```bash
     yum update -y
     ```

## Step 3: Install Apache HTTP Server

1. **Install Apache and Related Packages:**

   - Run the following command to install Apache HTTP Server, HTTPD tools, and SSL module:
     ```bash
     sudo yum install -y httpd httpd-tools mod_ssl
     ```

2. **Enable Apache to Start on Boot:**

   - Run the following command to enable Apache to start on boot:
     ```bash
     sudo systemctl enable httpd
     ```

3. **Start the Apache Service:**

   - Run the following command to start the Apache service:
     ```bash
     sudo systemctl start httpd
     ```

## Summary

You have successfully SSH'd into your EC2 instance, switched to the root user, updated all installed packages, and installed Apache HTTP Server.

- **SSH Command**: `ssh -i /path/to/your-key-pair.pem ec2-user@your-public-dns`
- **Switch to Root User**: `sudo su`
- **Update Packages**: `yum update -y`
- **Install Apache**:
  - `sudo yum install -y httpd httpd-tools mod_ssl`
- **Enable and Start Apache**:
  - `sudo systemctl enable httpd`
  - `sudo systemctl start httpd`

This setup ensures that your EC2 instance has Apache HTTP Server installed and running, ready to serve web content.

# Step-by-Step Guide to Install NFS Utilities and Mount an Amazon EFS File System

## Step 1: SSH into the EC2 Instance

1. **Open Terminal (Linux/Mac) or Command Prompt/PowerShell (Windows):**

   - On your local machine, open the terminal or command prompt.

2. **Navigate to the Directory with Your Key Pair File:**

   - Change to the directory where your `.pem` key pair file is located. Example:
     ```bash
     cd /path/to/your-key-pair.pem
     ```

3. **SSH into the Instance:**

   - Use the following command to SSH into your instance. Replace `/path/to/your-key-pair.pem` with the path to your `.pem` file and `your-public-dns` with the Public DNS name or IP address of your EC2 instance:
     ```bash
     ssh -i /path/to/your-key-pair.pem ec2-user@your-public-dns
     ```

   - Example:
     ```bash
     ssh -i /Users/username/keys/my-key-pair.pem ec2-user@ec2-54-123-45-67.compute-1.amazonaws.com
     ```

   - If this is your first time connecting to this instance, you may see a warning about the authenticity of the host. Type `yes` and press Enter to continue.

## Step 2: Run `sudo su` and `yum update -y`

1. **Switch to the Root User:**

   - Once connected, run the following command to switch to the root user:
     ```bash
     sudo su
     ```

2. **Update All Installed Packages:**

   - Run the following command to update all installed packages:
     ```bash
     yum update -y
     ```

## Step 3: Install NFS Utilities

1. **Install NFS Utilities:**

   - Run the following command to install NFS utilities:
     ```bash
     sudo yum install -y nfs-utils
     ```

## Step 4: Mount the Amazon EFS File System

1. **Create a Mount Point (if necessary):**

   - Ensure the directory `/var/www/html` exists. If it doesn't, create it:
     ```bash
     mkdir -p /var/www/html
     ```

2. **Mount the EFS File System:**

   - Run the following command to mount the EFS file system. Replace `fs-0f9c428d759181662.efs.us-east-1.amazonaws.com` with your EFS file system DNS name:
     ```bash
     sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-0f9c428d759181662.efs.us-east-1.amazonaws.com:/ /var/www/html
     ```

## Summary

You have successfully SSH'd into your EC2 instance, installed NFS utilities, and mounted an Amazon EFS file system.

- **SSH Command**: `ssh -i /path/to/your-key-pair.pem ec2-user@your-public-dns`
- **Switch to Root User**: `sudo su`
- **Update Packages**: `yum update -y`
- **Install NFS Utilities**: `sudo yum install -y nfs-utils`
- **Mount EFS File System**:
  - `sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-0f9c428d759181662.efs.us-east-1.amazonaws.com:/ /var/www/html`

This setup ensures that your EC2 instance has NFS utilities installed and the EFS file system mounted, allowing shared access to the file system.



## Step 2: Mount the EFS File System on WordPress Instances

1. **Install NFS Utilities:**
   - SSH into each of your WordPress instances.
   - Install the NFS client utilities.
     - For Amazon Linux, CentOS, RHEL:
       ```bash
       sudo yum install -y nfs-utils
       ```
     - For Ubuntu, Debian:
       ```bash
       sudo apt-get update
       sudo apt-get install -y nfs-common
       ```

2. **Create Mount Point:**
   - Create a directory to mount the EFS file system:
     ```bash
     sudo mkdir -p /mnt/efs
     ```

3. **Mount the EFS File System:**
   - Find the EFS DNS name from the EFS Dashboard (e.g., `fs-12345678.efs.us-west-2.amazonaws.com`).
   - Mount the EFS file system:
     ```bash
     sudo mount -t nfs4 -o nfsvers=4.1 fs-12345678.efs.us-west-2.amazonaws.com:/ /mnt/efs
     ```

4. **Update `/etc/fstab` for Automatic Mounting:**
   - Open `/etc/fstab` in an editor:
     ```bash
     sudo nano /etc/fstab
     ```
   - Add the following line to ensure the EFS mounts automatically after a reboot:
     ```bash
     fs-12345678.efs.us-west-2.amazonaws.com:/ /mnt/efs nfs4 defaults,_netdev 0 0
     ```
   - Save and close the file.

## Step 3: Configure WordPress to Use the Shared File System

1. **Move WordPress Files to EFS:**
   - Move the `wp-content` directory to the EFS mount point:
     ```bash
     sudo mv /var/www/html/wp-content /mnt/efs/wp-content
     ```
   - Create a symbolic link in the original location:
     ```bash
     sudo ln -s /mnt/efs/wp-content /var/www/html/wp-content
     ```

2. **Set Correct Permissions:**
   - Ensure the web server has the appropriate permissions to access the EFS directory:
     ```bash
     sudo chown -R www-data:www-data /mnt/efs/wp-content
     sudo chmod -R 755 /mnt/efs/wp-content
     ```
   - Replace `www-data:www-data` with your web server's user and group if different.

3. **Restart Web Server:**
   - Restart the web server to apply changes:
     - For Apache:
       ```bash
       sudo systemctl restart apache2
       ```
     - For Nginx:
       ```bash
       sudo systemctl restart nginx
       ```

## Summary

You have successfully configured Amazon Elastic File System (EFS) for storing WordPress files, allowing scalable and shared access across multiple WordPress instances.

- **EFS File System**: `wordpress-efs`
- **Mount Point on Instances**: `/mnt/efs`
- **WordPress Directory Moved**: `wp-content` moved to `/mnt/efs/wp-content` and symlinked back
- **Permissions**: Set for web server access

This setup ensures that your WordPress instances can share the same file system, providing scalability and consistency across multiple instances.


