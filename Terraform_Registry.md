 Here's a **step-by-step guide on how to use the Terraform Registry**—the official source for publicly available Terraform modules and providers.

---

# 📚 How to Use the Terraform Registry

The [Terraform Registry](https://registry.terraform.io/) helps you **discover**, **use**, and **reuse** community-contributed **modules** and **providers** in your Terraform projects.

---

## ✅ What You Can Find in the Terraform Registry

1. **Providers** – Plugins for platforms (AWS, Azure, GCP, GitHub, etc.)
2. **Modules** – Reusable Terraform configurations (e.g., VPC, EC2, EKS)

---

## 🔧 Step-by-Step Instructions to Use Terraform Registry

---

### 🟩 Step 1: Visit the Terraform Registry

👉 URL: [https://registry.terraform.io](https://registry.terraform.io)

* Search for a **provider** or **module** (e.g., `aws vpc`)

---

### 🟩 Step 2: Copy the Usage Snippet

Each module has an “**Usage**” example on its page.

For example, [AWS VPC module](https://registry.terraform.io/modules/terraform-aws-modules/vpc/aws/latest):

```hcl
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "5.1.2"

  name = "my-vpc"
  cidr = "10.0.0.0/16"

  azs             = ["us-west-2a", "us-west-2b"]
  public_subnets  = ["10.0.1.0/24", "10.0.2.0/24"]
  private_subnets = ["10.0.3.0/24", "10.0.4.0/24"]
}
```

> You can copy this directly into your `main.tf`.

---

### 🟩 Step 3: Add `terraform` block to declare required providers (if not already)

```hcl
terraform {
  required_version = ">= 1.3.0"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}
```

---

### 🟩 Step 4: Initialize the Module

In your project directory:

```bash
terraform init
```

This downloads the module and its dependencies.

---

### 🟩 Step 5: Plan and Apply

```bash
terraform plan
terraform apply
```

Follow prompts and type `yes` to confirm apply.

---

### 🟩 Step 6: Explore Outputs & Manage

Modules often provide built-in outputs. You can reference them like:

```hcl
output "vpc_id" {
  value = module.vpc.vpc_id
}
```

Check the module documentation to see available inputs & outputs.

---

## 🧠 Best Practices for Using Terraform Registry Modules

| Practice             | Why it Matters                                          |
| -------------------- | ------------------------------------------------------- |
| ✅ Pin version        | Avoid breaking changes                                  |
| ✅ Read README        | Each module has usage docs, inputs, outputs             |
| ✅ Use variables      | Avoid hardcoding sensitive or environment-specific data |
| ✅ Validate your code | Always run `terraform validate` or `tflint`             |
| ✅ Check source       | Review the module source code for quality & security    |

---

## 🧪 Example Folder Structure Using Registry Module

```
terraform-vpc/
├── main.tf             # Where you include the module
├── variables.tf        # Your input variables
├── terraform.tfvars    # Your variable values
└── outputs.tf          # Outputs from the module
```

---

## 📦 Want to Publish Your Own Module?

1. Create a public GitHub repo
2. Follow naming convention: `terraform-<PROVIDER>-<NAME>`
3. Tag with `v1.0.0` or higher
4. Add `README.md`, `main.tf`, `variables.tf`, and `outputs.tf`
5. It will be automatically detected by the Registry

---
 Here's a **template Terraform project** that uses a **Terraform Registry module**—specifically the popular `terraform-aws-modules/vpc/aws` module—to create a **custom VPC on AWS**.

---

# 📁 Template Terraform Project Using Registry Module (AWS VPC)

---

## 🗂️ Folder Structure

```
terraform-vpc-project/
├── main.tf
├── variables.tf
├── terraform.tfvars
├── outputs.tf
└── backend.tf         (optional, for remote state)
```

---

## 📄 `main.tf`

```hcl
terraform {
  required_version = ">= 1.3.0"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = var.aws_region
}

module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "5.1.2"

  name = var.vpc_name
  cidr = var.vpc_cidr

  azs             = var.availability_zones
  public_subnets  = var.public_subnets
  private_subnets = var.private_subnets

  enable_nat_gateway = true
  enable_dns_hostnames = true
  enable_dns_support   = true

  tags = {
    Environment = var.environment
    Terraform   = "true"
  }
}
```

---

## 📄 `variables.tf`

```hcl
variable "aws_region" {
  type    = string
  default = "us-west-2"
}

variable "vpc_name" {
  type    = string
  default = "my-vpc"
}

variable "vpc_cidr" {
  type    = string
  default = "10.0.0.0/16"
}

variable "availability_zones" {
  type    = list(string)
  default = ["us-west-2a", "us-west-2b"]
}

variable "public_subnets" {
  type    = list(string)
  default = ["10.0.1.0/24", "10.0.2.0/24"]
}

variable "private_subnets" {
  type    = list(string)
  default = ["10.0.3.0/24", "10.0.4.0/24"]
}

variable "environment" {
  type    = string
  default = "dev"
}
```

---

## 📄 `terraform.tfvars`

```hcl
aws_region         = "us-west-2"
vpc_name           = "demo-vpc"
vpc_cidr           = "10.0.0.0/16"
availability_zones = ["us-west-2a", "us-west-2b"]
public_subnets     = ["10.0.1.0/24", "10.0.2.0/24"]
private_subnets    = ["10.0.3.0/24", "10.0.4.0/24"]
environment        = "dev"
```

---

## 📄 `outputs.tf`

```hcl
output "vpc_id" {
  value = module.vpc.vpc_id
}

output "private_subnets" {
  value = module.vpc.private_subnets
}

output "public_subnets" {
  value = module.vpc.public_subnets
}
```

---

## (Optional) 📄 `backend.tf` — Remote State in S3

```hcl
terraform {
  backend "s3" {
    bucket         = "my-terraform-state-bucket"
    key            = "dev/vpc.tfstate"
    region         = "us-west-2"
    dynamodb_table = "terraform-locks"
    encrypt        = true
  }
}
```

---

## 🚀 How to Use the Template

```bash
# Clone or copy the folder
cd terraform-vpc-project

# Initialize and download module
terraform init

# Preview the plan
terraform plan -var-file="terraform.tfvars"

# Apply the changes
terraform apply -var-file="terraform.tfvars"
```

---

## 🧼 Cleanup

```bash
terraform destroy -var-file="terraform.tfvars"
```

---



