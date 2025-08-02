**complete step-by-step guide for DevOps students** on how to **create an Amazon EKS (Elastic Kubernetes Service) cluster**
---

# ☁️ **Amazon EKS Setup Guide (Step-by-Step with Explanations)**

## 🧑‍🏫 Target Audience:

* DevOps students & practitioners
* Learning goal: Deploy and manage Kubernetes on AWS using EKS

---

## ✅ Step 1: Create an AWS Account

### 1. Go to [https://aws.amazon.com](https://aws.amazon.com)

* Click **Create an AWS Account**
* Enter your **email, password, and account name**
* Provide **billing details** (free tier available)
* Complete **identity verification**
* Choose the **Basic Support Plan (free)**

🔐 **Note**: Store your AWS credentials safely.

---

## ✅ Step 2: Set Up IAM User for CLI Access

### 1. Sign in to AWS Console as the root user

### 2. Go to **IAM (Identity & Access Management)**:

* Create a new **IAM user** (e.g., `eks-admin`)
* Enable **Programmatic access**
* Attach **AdministratorAccess** policy
* Download your **Access Key ID and Secret Access Key**

---
Here are the **step-by-step instructions** to:

✅ **Create a new IAM user (e.g., `eks-admin`)**
✅ **Enable Programmatic Access**
✅ **Attach `AdministratorAccess` policy**

---

### 🔐 Step 1: Log in to AWS Console

1. Go to [https://console.aws.amazon.com/iam](https://console.aws.amazon.com/iam)
2. Make sure you are in the correct **AWS account and region**

---

### 👤 Step 2: Create a New IAM User

1. In the left sidebar, click **Users**
2. Click the **“Add users”** button
3. Under **User name**, enter: `eks-admin`
4. Under **Select AWS credential type**:

   * ✅ Check **Programmatic access** (for CLI/SDK access)
   * ❌ You may uncheck **Console access** unless you also want web UI login
5. Click **Next: Permissions**

---

### 🔐 Step 3: Set Permissions

1. Choose **“Attach policies directly”**
2. In the search box, type: `AdministratorAccess`
3. Check the box next to **AdministratorAccess** (this grants full permissions)

> 🔒 **Caution**: This gives full access to all AWS services. Use with trusted users only.

4. Click **Next: Tags** (optional, you can skip or add tags)
5. Click **Next: Review**

---

### ✅ Step 4: Review and Create User

1. Confirm the user name is `eks-admin`
2. Confirm **Programmatic access** is enabled
3. Confirm `AdministratorAccess` policy is attached
4. Click **Create user**

---

### 📄 Step 5: Save Access Credentials

1. On the success page, you will see:

   * **Access key ID**
   * **Secret access key**

> ⚠️ Make sure to **download the .csv file** or copy these credentials immediately. You **will not see the secret again**.

---
## ✅ Step 3: Configure AWS CLI on Your Machine

### 1. Install AWS CLI

```bash
# macOS
brew install awscli

# Ubuntu
sudo apt-get install awscli -y
```
You're seeing this error because the `awscli` package is not available from the default Ubuntu repositories in your current configuration. Here's how to correctly install the **latest AWS CLI v2** on Ubuntu:

---

### ✅ **Steps to Install AWS CLI v2 on Ubuntu**

1. **Update your system packages**

```bash
sudo apt update && sudo apt upgrade -y
```

2. **Install required dependencies**

```bash
sudo apt install unzip curl -y
```

3. **Download the AWS CLI v2 installer**

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
```

4. **Unzip the installer**

```bash
unzip awscliv2.zip
```

5. **Run the install script**

```bash
sudo ./aws/install
```

6. **Verify the installation**

```bash
aws --version
```

You should see something like:

```bash
aws-cli/2.x.x Python/3.x.x Linux/x86_64 source
```

---



### 2. Configure your CLI credentials

```bash
aws configure
```

> You will be prompted to enter:

* Access Key ID
* Secret Access Key
* Region (e.g., `us-west-2`)
* Output format (use `json`)

---
