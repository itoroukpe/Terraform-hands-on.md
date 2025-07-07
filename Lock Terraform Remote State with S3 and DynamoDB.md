Locking Terraform remote state using **S3** (to store the state file) and **DynamoDB** (to lock it and avoid concurrency issues) is a **best practice** for team collaboration and environment safety.

Here’s a **step-by-step guide** on how to lock Terraform state with **Amazon S3 and DynamoDB**.

---

# 🔐 Step-by-Step: Lock Terraform Remote State with S3 + DynamoDB

---

## ✅ What You’ll Do

* ✅ Create an S3 bucket to store state
* ✅ Create a DynamoDB table for state locking
* ✅ Configure Terraform backend to use them

---

## 🧰 Prerequisites

* AWS CLI installed and configured
* IAM User with permission to create S3 and DynamoDB
* Terraform v1.1+ installed

---

## 🔧 Step 1: Create the S3 Bucket

### 🛠️ Option 1: AWS CLI

```bash
aws s3api create-bucket \
  --bucket my-terraform-state-bucket-123456 \
  --region us-west-2 \
  --create-bucket-configuration LocationConstraint=us-west-2
```

Make the bucket private and versioned:

```bash
aws s3api put-bucket-versioning \
  --bucket my-terraform-state-bucket-123456 \
  --versioning-configuration Status=Enabled
```

---

## 📦 Step 2: Create DynamoDB Table

```bash
aws dynamodb create-table \
  --table-name terraform-locks \
  --attribute-definitions AttributeName=LockID,AttributeType=S \
  --key-schema AttributeName=LockID,KeyType=HASH \
  --provisioned-throughput ReadCapacityUnits=1,WriteCapacityUnits=1 \
  --region us-west-2
```

> `LockID` is used by Terraform to uniquely lock state operations.

---

## 📁 Step 3: Configure Terraform Backend

In your Terraform environment folder (e.g. `envs/dev/`), create a `backend.tf` file:

### ✅ `backend.tf`

```hcl
terraform {
  backend "s3" {
    bucket         = "my-terraform-state-bucket-123456"
    key            = "dev/terraform.tfstate"
    region         = "us-west-2"
    dynamodb_table = "terraform-locks"
    encrypt        = true
  }
}
```

---

## ⚙️ Step 4: Initialize Terraform Backend

```bash
terraform init
```

You'll be prompted:

```
Do you want to copy existing state to the new backend?
```

Type `yes`.

---

## ✅ Step 5: Verify Locking in Action

Open two terminals:

### Terminal 1:

```bash
terraform apply
```

While the first apply is running...

### Terminal 2:

```bash
terraform apply
```

You should see:

```
Error acquiring the state lock: ConditionalCheckFailedException
```

✅ This confirms the lock is working via DynamoDB.

---

## 🧠 Notes & Best Practices

| Component   | Recommendation                                                         |
| ----------- | ---------------------------------------------------------------------- |
| S3 Bucket   | Enable **versioning** to recover corrupted state                       |
| DynamoDB    | Use **LockID** as hash key for state file locks                        |
| Permissions | Grant only `GetObject`, `PutObject`, and DynamoDB `GetItem`, `PutItem` |
| CI/CD       | Automate with GitHub Actions or GitLab CI                              |

---

## 🎁 Want Extras?

I can generate for you:

✅ A working **starter repo** with this setup
✅ A **PowerPoint walkthrough**
✅ GitHub Actions **CI/CD workflow** using this backend
✅ Terraform **IAM policy** for least privilege


