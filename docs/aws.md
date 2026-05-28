---
layout: default
title: AWS
date:   2021-09-11 20:28:05 -0400
categories: Linux
nav_order: 101

---

---
{: .no_toc }

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

# Simple example to build a private EC2 instance that can access the internet only through a NAT Gateway (no public IP on the instance). Comes from DeepSeek result

Below is a simple, step‑by‑step example you can complete in the AWS Console. It creates a **private EC2 instance** that can access the internet **only through a NAT Gateway** (no public IP on the instance).

The total time is about **15–20 minutes**, and most of the resources are eligible for the AWS Free Tier.

---

## 🎯 What You Will Build

- A **VPC** with two subnets:
  - **Public subnet** (contains the NAT Gateway)
  - **Private subnet** (contains an EC2 instance with no public IP)
- A **NAT Gateway** in the public subnet (with an Elastic IP)
- A **private EC2 instance** (Amazon Linux) that can reach the internet through the NAT Gateway

After the setup, you will verify the private instance can access the internet by running `curl ifconfig.me` from it.

---

## 🧱 Step 1: Create the VPC and Subnets

### 1.1 Create a VPC
1. Open the **VPC Console** → **Your VPCs** → **Create VPC**
2. Select **VPC only**
3. **Name tag**: `NatLab-VPC`
4. **IPv4 CIDR**: `10.0.0.0/16`
5. Click **Create VPC**

### 1.2 Create the Public Subnet
1. In the left menu, go to **Subnets** → **Create subnet**
2. **VPC ID**: Select `NatLab-VPC`
3. **Subnet name**: `Public-Subnet`
4. **Availability Zone**: Choose any (e.g., `us-east-1a`)
5. **IPv4 CIDR**: `10.0.1.0/24`
6. Click **Create subnet**

### 1.3 Create the Private Subnet
1. Click **Create subnet** again
2. **VPC ID**: Select `NatLab-VPC`
3. **Subnet name**: `Private-Subnet`
4. **Availability Zone**: Use the **same AZ** as the public subnet (important for this simple lab)
5. **IPv4 CIDR**: `10.0.2.0/24`
6. Click **Create subnet**

> 📌 Using the same Availability Zone ensures the NAT Gateway (in the public subnet) can route traffic to the private subnet without extra charges or complexity.

### 1.4 Create an Internet Gateway and Attach It
1. Go to **Internet Gateways** → **Create internet gateway**
2. **Name tag**: `NatLab-IGW`
3. Click **Create internet gateway**
4. Select the IGW → **Actions** → **Attach to VPC** → choose `NatLab-VPC` → **Attach**

### 1.5 Create Route Tables

**Public Route Table**
1. Go to **Route Tables** → **Create route table**
2. **Name**: `Public-RT`
3. **VPC**: `NatLab-VPC`
4. Click **Create**
5. Select `Public-RT` → **Routes** tab → **Edit routes** → **Add route**:
   - Destination: `0.0.0.0/0`
   - Target: `NatLab-IGW` (the Internet Gateway)
6. Click **Save changes**
7. **Subnet associations** tab → **Edit subnet associations** → select `Public-Subnet` → **Save**

**Private Route Table (no internet route yet)**
1. Go to **Route Tables** → **Create route table**
2. **Name**: `Private-RT`
3. **VPC**: `NatLab-VPC`
4. Click **Create**
5. **Subnet associations** tab → **Edit subnet associations** → select `Private-Subnet` → **Save**

> The private route table has **no `0.0.0.0/0` route** for now. Later, you will point that route to the NAT Gateway.

---

## 🔁 Step 2: Create a NAT Gateway (in the Public Subnet)

1. Go to **NAT Gateways** → **Create NAT gateway**
2. **Name**: `NatLab-NAT`
3. **Subnet**: Select `Public-Subnet`
4. **Connectivity type**: **Public**
5. **Elastic IP allocation**: Click **Allocate Elastic IP** (this creates a new EIP)
6. Click **Create NAT gateway**

> ⏱️ The NAT Gateway takes **1–2 minutes** to become available. Wait for the status to change from `pending` to `available` before moving to the next step.

---

## 🔗 Step 3: Update the Private Route Table to Use the NAT Gateway

1. Go to **Route Tables** → select `Private-RT`
2. **Routes** tab → **Edit routes** → **Add route**:
   - Destination: `0.0.0.0/0`
   - Target: Select **NAT Gateway** → choose `NatLab-NAT`
3. Click **Save changes**

Now any instance in `Private-Subnet` will use the NAT Gateway for internet access.

---

## 🖥️ Step 4: Launch a Private EC2 Instance (No Public IP)

1. Open the **EC2 Console** → **Launch instance**
2. **Name**: `Private-Instance`
3. **AMI**: Amazon Linux 2023 (Free Tier eligible)
4. **Instance type**: `t2.micro` (Free Tier eligible)
5. **Key pair**: Either create a new one or select an existing key (you will need it to SSH later)
6. **Network settings** → **Edit**:
   - **VPC**: `NatLab-VPC`
   - **Subnet**: `Private-Subnet`
   - **Auto-assign public IP**: **Disable** (important!)
   - **Firewall (security group)** : Create a new security group with:
     - **SSH (port 22)** from `0.0.0.0/0` (or your IP)
     - **HTTP/HTTPS** not required for this test
7. **Advanced details** → **User data** (paste the script below to install curl and show the public IP at launch):

```bash
#!/bin/bash
yum install -y curl
```

8. Click **Launch instance**

---

## 🔑 Step 5: Connect to the Private Instance

Because the private instance has **no public IP**, you cannot SSH to it directly from the internet. The simplest way to connect is using **EC2 Instance Connect** if your AMI supports it, or you can use a **bastion host**. For this lab, we will use **AWS Systems Manager Session Manager** (no open inbound ports required).

### 5.1 Attach the SSM Managed Instance Core Policy
1. Go to **IAM Console** → **Roles** → **Create role**
2. Trusted entity: **AWS service** → **EC2** → **Next**
3. Search for and attach: `AmazonSSMManagedInstanceCore`
4. Role name: `SSM-Role-For-EC2`
5. Click **Create role**
6. Go back to the EC2 Console, select `Private-Instance` → **Actions** → **Security** → **Modify IAM role** → choose `SSM-Role-For-EC2` → **Update IAM role**
7. **Restart the instance (Important!)** : The SSM Agent may not pick up the new role immediately. A restart forces it to refresh its credentials .Select your instance → Instance state → Reboot


### 5.2 Connect Using Session Manager
1. Go to **Systems Manager Console** → **Session Manager**
2. Click **Start session**
3. Select `Private-Instance` → **Start session**

You are now inside the private instance’s terminal.

---

## ✅ Step 6: Verify Internet Access Through the NAT Gateway

Inside the Session Manager terminal, run:

```bash
curl ifconfig.me
```

This command returns the public IP address of the NAT Gateway (the Elastic IP you allocated). If you see a valid public IP (e.g., `54.123.45.67`), **the private instance has successfully reached the internet through the NAT Gateway**.

For more confidence, also run:

```bash
curl https://aws.amazon.com
```

You should see the HTML of the AWS homepage.

---

## 🧹 Step 7: Clean Up (Avoid Unexpected Charges)

1. **Terminate the private instance** (EC2 Console → select instance → **Instance state** → **Terminate**)
2. **Delete the NAT Gateway** (NAT Gateways → select → **Actions** → **Delete NAT Gateway**). Wait for deletion to complete (a few minutes).
3. **Release the Elastic IP** (Elastic IPs → select → **Actions** → **Release Elastic IP**)
4. **Delete the VPC** (VPC Console → Your VPCs → select `NatLab-VPC` → **Actions** → **Delete VPC**). This automatically deletes subnets, route tables, security groups, and the internet gateway.

> ⚠️ Do not skip the NAT Gateway deletion. Leaving a NAT Gateway running will incur an hourly charge (~$0.045/hour). The other resources (VPC, subnets, etc.) are free.

---

## 📊 Summary of What You Have Accomplished

| Resource | Private Instance? | Internet Access | How |
| :--- | :--- | :--- | :--- |
| **Public Subnet** | No | Direct (via IGW) | Route `0.0.0.0/0 → igw` |
| **Private Subnet** | Yes (this instance) | Yes (outbound only) | Route `0.0.0.0/0 → nat` + NAT Gateway in public subnet |
| **NAT Gateway** | No | Yes (public IP) | Translates private IPs → its own public IP |

The private instance **cannot be reached from the internet** (no public IP, no inbound NAT rule), but it **can initiate connections to the internet** through the NAT Gateway.

---

## 💡 What to Try Next

- Launch a second private instance in a **different Availability Zone** and observe that the NAT Gateway (which is zonal) would require you to create another NAT Gateway in that AZ for high availability.
- Replace the NAT Gateway with a **NAT Instance** (a self‑managed EC2) to see the cost difference.
- Add a **Gateway VPC Endpoint** for S3 so that traffic to S3 bypasses the NAT Gateway (saving data processing costs).

Would you like a version of this lab using **AWS CloudFormation** or **Terraform**?

# How to get the ip address

```bash
curl http://checkip.amazonaws.com/

curl ifconfig.me
```

Or just paste the address `http://checkip.amazonaws.com/` or `ifconfig.me` in the browser.


# A simple EC2 web to show the instance IP address

This template will boot a simple web server.

- Navigate to EC2 → Launch Templates → Create launch template.

- Name: WebServer-LT

- AMI: Select Amazon Linux 2 AMI (HVM) (Free tier eligible).

- Instance type: Select t2.micro (Free tier eligible).

- Key pair (login): Select an existing key pair or create a new one to SSH into the instances later.

- Network settings: Leave as default (the launch template will be associated with a security group later).

- Advanced details → User data: Copy and paste the script below. This installs Apache and creates a custom webpage.

```bash
#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "<h1>Hello from $(hostname -f)</h1>" > /var/www/html/index.html
```

Or use the Apache web server

```bash
#!/bin/bash
apt-get update
apt-get install -y apache2
echo "Welcome to my website"> index.html
cp index.html /var/www/html
```


# Configure lifecycle configuration and use the filter to apply the policy to specific folder

The following comes from Google AI
> In S3 lifecycle configurations, you define a prefix within a Filter block (XML) or a filter argument (Terraform) to target specific folders or subsets of objects. The prefix acts as a key-value folder path (e.g., logs/ or data/archive/). If omitted, the rule applies to the entire bucket

Key Considerations:
- No leading slash: Do not start the prefix with a `/` (e.g., use `folder/`, not `/folder/`).
- Case Sensitivity: Prefixes are case-sensitive.
- Empty Prefix: An empty prefix ("") applies the rule to all objects in the bucket.

  
# Connect two aws account in one on-premise server
1. Run aws configure `--profile <profile-name>` for each account, providing unique access keys, secret keys, default regions, and output formats.
2. Usage: Specify the desired profile with the `--profile` flag in your AWS commands (e.g., `aws s3 ls --profile AccountBProfile`) or set the AWS_DEFAULT_PROFILE environment variable for a session. 

# [aws commands comes from AWS Cookbook]

- Detach the policy from the role before deleting the role
   
   ```bash
  aws iam detach-role-policy --role-name AWSCookbook504Role \
  --policy-arn arn:aws:iam::aws:policy/service-role/AWSLambdaVPCAccessExecutionRole


  aws iam delete-role --role-name AWSCookbook504Role
   ```
- Remove the Ingress rule from the security group before deleting the security group
  ```
  aws ec2 revoke-security-group-ingress \
  --protocol tcp --port 2049 \
  --source-group $LAMBDA_SG_ID \
  --group-id $EFS_SECURITY_GROUP

  aws ec2 delete-security-group --group-id $LAMBDA_SG_ID
  ```
- Create a random string
  ```bash
  RANDOM_STRING=$(aws secretsmanager get-random-password \
  --exclude-punctuation --exclude-uppercase \
  --password-length 6 --require-each-included-type \
  --output text \
  --query RandomPassword)
  ```
- 


# [aws structure based on my thought]

In all, AWS is build by the following parts:
1. IAM : provide security 
2. EC2 : is a service of AWS and as the base of aws other services like Lambda, RMD
3. Network: connect aws services and transport the information between services.


Security Group(SG) provide security for indivisual resource( e.g.: Lambda, EC2) in aws. It can control the network connectivity using ingress or outgress and port number.

The resource is an instance of service.

# [aws Cloud Develop Kit documentation](https://docs.aws.amazon.com/cdk/api/v2/python/aws_cdk.aws_rds/MysqlEngineVersion.html)

You can find the libary of aws CDK.



# Connect the EC2 instance using the CLI

```bash
aws ssm start-session --target $INSTANCE_ID
```

# [Concept: What's the CIDR(Classless Inter-Domain Routing)](https://www.youtube.com/watch?v=KiWXRL-2TnY)

From the Google Search:
>CIDR notation is a compact way to express an IP address and its associated network by following it with a slash and a number indicating how many bits are used for the network prefix
>
>The format is IP_ADDRESS/PREFIX_LENGTH.
>
>For example, in 192.168.1.0/24, 192.168.1.0 is the IP address, and /24 is the prefix length. 

The IP Address is 32 bits. There are four parts seperated by dot(.) and each part has 8 bits. So each part has 2**8=256 different numbers. As it starts from 0, so the range is 0- 255.

For example, 10.0.0.0/16 represents all IP addresses in 10.0.0.0 and 10.0.255.255. Why? 32 bits - 16 bits = 16 bits, so the first two parts (2*8 = 16) is prefix address. the left two parts can be used for the subnet address. so the IP addresses can be used from  10.0.0.0 to 10.0.255.255.

If the subnet is 10.0.0.0/24, then all IP addresses is in 10.0.0.0 and 10.0.0.255. Because only 8 bits left for the subnet.

# [AWS CLI to restore an Amazon S3 object from the S3 Glacier Flexible Retrieval or S3 Glacier Deep Archive storage class](https://repost.aws/knowledge-center/restore-s3-object-glacier-storage-class)

```bash
$ aws s3api restore-object --bucket awsexamplebucket --key dir1/example.obj --restore-request '{"Days":25,"GlacierJobParameters":{"Tier":"Standard"}}'

```

# Copy local file to AWS S3

When you transfer data to AWS S3, you need to use the following code
```bash
aws s3 cp "${pathLocalFile}" s3://"$bucketName/$bucketFolder/" --sse AES256 --no-progress
```
Here you need to pay attention to the following stuff:
- you need to add backslash at the end of the bucket folder or backet name `/`. So the command line know it's a folder and save the local file to the folder, otherwise it will be treated as a file name that you want to name the local file in the AWS S3
- it's safe to use double quote `"` to enclose the variable when using the command line in case the path of varialbe contains empty space which will make the command line volid
