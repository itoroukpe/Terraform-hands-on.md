**step-by-step hands-on Terraform tutorial** designed for **DevOps students**. 
This guide assumes basic familiarity with the terminal and cloud platforms like AWS.

---

# 🚀 Terraform Hands-On Tutorial for DevOps Students

## ✅ Objective:

Learn how to use Terraform to:

* Set up infrastructure as code (IaC)
* Create an AWS EC2 instance
* Understand Terraform workflow (init, plan, apply, destroy)

---

## 🧰 Prerequisites

1. **AWS Account** (Free Tier is sufficient)
2. **IAM User** with programmatic access & permissions
3. **AWS CLI Installed**
4. **Terraform Installed** ([https://developer.hashicorp.com/terraform/downloads](https://developer.hashicorp.com/terraform/downloads))
5. A **code editor** (e.g., VS Code)

---

## 📁 Project Setup

### Step 1: Create a working directory

```bash
mkdir terraform-demo
cd terraform-demo
```

---

## 📄 Step 2: Create a Terraform Configuration File

Create a file named `main.tf`:

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "example" {
  ami           = "ami-0c02fb55956c7d316"  # Amazon Linux 2
  instance_type = "t2.micro"

  tags = {
    Name = "TerraformDemoInstance"
  }
}
```

> 📌 This defines a single EC2 instance in AWS using Amazon Linux 2.

---

## 🔐 Step 3: Configure AWS Credentials

You can do this via the AWS CLI:

```bash
aws configure
```

Provide:

* AWS Access Key ID
* AWS Secret Access Key
* Default region: `us-west-2`
* Output format: `json`

---

## ⚙️ Step 4: Initialize Terraform

```bash
terraform init
```

> This command sets up your working directory and downloads the required provider plugins.

---

## 🔍 Step 5: Review What Terraform Will Do

```bash
terraform plan
```

> This shows a preview of what Terraform will create or change.

---

## 🚀 Step 6: Apply Configuration

```bash
terraform apply
```

Type `yes` when prompted.

> This will **create the EC2 instance** in AWS.

---

## ✅ Step 7: Verify the Infrastructure

* Go to the AWS Console → EC2 → Instances
* You’ll see a running instance named **TerraformDemoInstance**

---

## 🧹 Step 8: Destroy Infrastructure

```bash
terraform destroy
```

Type `yes` when prompted.

> This cleans up all resources that Terraform created.

---

## 📦 Bonus Step: Organizing Terraform Code

Split the code into multiple files:

* `main.tf` – core resources
* `provider.tf` – provider config
* `variables.tf` – declare variables
* `terraform.tfvars` – assign variable values
* `outputs.tf` – define outputs like instance IP

Example `outputs.tf`:

```hcl
output "instance_ip" {
  value = aws_instance.example.public_ip
}
```

---

## 📘 What You Learned

* What Terraform is and why DevOps teams use it
* How to write Terraform configuration
* How to manage cloud infrastructure using CLI
* Terraform commands: `init`, `plan`, `apply`, `destroy`

---

## 🧠 Recommended Next Steps

1. Create an S3 bucket with Terraform
2. Use `terraform modules`
3. Manage state with remote backends
4. Automate with GitHub Actions
5. Integrate with other tools like Ansible, Helm, Jenkins

---


