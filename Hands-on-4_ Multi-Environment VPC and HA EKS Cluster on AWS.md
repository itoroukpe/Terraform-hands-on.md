 **step-by-step, hands-on Terraform guide** to create a **complete VPC and Highly Available EKS Cluster on AWS** for **multiple environments**: `Dev`, `QA`, `Staging`, `Production`, and `Disaster Recovery (DR)`.

This tutorial follows best practices for **environment isolation**, **modular Terraform**, and **reusability**.

---

# 🚀 Terraform Hands-On: Multi-Environment VPC & HA EKS Cluster on AWS

---

## ✅ What You’ll Build

For **each environment** (`dev`, `qa`, `staging`, `prod`, `dr`), you will:

* Provision an **isolated VPC**
* Create **multi-AZ public & private subnets**
* Deploy a **highly available EKS cluster** using **Terraform modules**

---

## 🧰 Prerequisites

* AWS CLI configured (`aws configure`)
* Terraform v1.3+ installed
* `kubectl` installed
* IAM user with sufficient privileges
* Git & text editor (e.g., VS Code)

---

## 🗂️ Directory Structure

```bash
terraform-multi-env-eks/
│
├── modules/
│   ├── vpc/
│   │   └── main.tf, variables.tf, outputs.tf
│   └── eks/
│       └── main.tf, variables.tf, outputs.tf
│
├── envs/
│   ├── dev/
│   │   └── main.tf, terraform.tfvars, backend.tf
│   ├── qa/
│   ├── staging/
│   ├── prod/
│   └── dr/
│
├── variables.tf
└── README.md
```

Each environment folder will reuse shared modules.

---

## 🔧 Step-by-Step Setup

---

### ✅ Step 1: Create the Modules

#### 📁 `modules/vpc/main.tf`

```hcl
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "5.1.2"

  name                 = var.vpc_name
  cidr                 = var.vpc_cidr
  azs                  = var.azs
  public_subnets       = var.public_subnets
  private_subnets      = var.private_subnets
  enable_nat_gateway   = true
  single_nat_gateway   = true
  enable_dns_support   = true
  enable_dns_hostnames = true

  tags = var.tags
}
```

#### 📁 `modules/vpc/variables.tf`

```hcl
variable "vpc_name" {}
variable "vpc_cidr" {}
variable "azs" {}
variable "public_subnets" {}
variable "private_subnets" {}
variable "tags" {}
```

#### 📁 `modules/vpc/outputs.tf`

```hcl
output "vpc_id" {
  value = module.vpc.vpc_id
}

output "private_subnets" {
  value = module.vpc.private_subnets
}
```

---

#### 📁 `modules/eks/main.tf`

```hcl
module "eks" {
  source          = "terraform-aws-modules/eks/aws"
  version         = "20.4.0"

  cluster_name    = var.cluster_name
  cluster_version = "1.28"
  subnet_ids      = var.subnet_ids
  vpc_id          = var.vpc_id
  enable_irsa     = true

  eks_managed_node_groups = {
    default = {
      desired_size = 2
      max_size     = 3
      min_size     = 1

      instance_types = ["t3.medium"]
    }
  }

  tags = var.tags
}
```

#### 📁 `modules/eks/variables.tf`

```hcl
variable "cluster_name" {}
variable "vpc_id" {}
variable "subnet_ids" {}
variable "tags" {}
```

#### 📁 `modules/eks/outputs.tf`

```hcl
output "cluster_name" {
  value = module.eks.cluster_name
}

output "cluster_endpoint" {
  value = module.eks.cluster_endpoint
}
```

---

### ✅ Step 2: Create the Dev Environment

#### 📁 `envs/dev/backend.tf`

```hcl
terraform {
  backend "s3" {
    bucket = "my-terraform-state"
    key    = "dev/terraform.tfstate"
    region = "us-west-2"
  }
}
```

#### 📁 `envs/dev/main.tf`

```hcl
provider "aws" {
  region = var.region
}

module "vpc" {
  source         = "../../modules/vpc"
  vpc_name       = "dev-vpc"
  vpc_cidr       = "10.10.0.0/16"
  azs            = ["us-west-2a", "us-west-2b"]
  public_subnets = ["10.10.1.0/24", "10.10.2.0/24"]
  private_subnets = ["10.10.3.0/24", "10.10.4.0/24"]
  tags           = { Environment = "dev" }
}

module "eks" {
  source       = "../../modules/eks"
  cluster_name = "dev-eks-cluster"
  vpc_id       = module.vpc.vpc_id
  subnet_ids   = module.vpc.private_subnets
  tags         = { Environment = "dev" }
}
```

#### 📁 `envs/dev/terraform.tfvars`

```hcl
region = "us-west-2"
```

---

### ✅ Step 3: Repeat for Other Environments

📁 `envs/qa`, `envs/staging`, `envs/prod`, `envs/dr` — follow same structure as `dev`, but update:

* VPC CIDR ranges
* Subnet IP ranges
* Tags and names
* Cluster names

---

### ✅ Step 4: Initialize and Apply Each Environment

```bash
cd envs/dev
terraform init
terraform plan
terraform apply
```

Repeat for `qa`, `staging`, `prod`, and `dr` folders.

---

## 🧪 Validate the EKS Cluster

After deploying:

```bash
aws eks --region us-west-2 update-kubeconfig --name dev-eks-cluster
kubectl get nodes
```

Do the same for other environments by switching kubeconfigs.

---

## 🧹 Cleanup

```bash
terraform destroy
```

---

## 🧠 Best Practices

* Use **Terraform workspaces** if you prefer a single configuration
* Lock remote state with **S3 + DynamoDB**
* Automate deployments via **CI/CD pipelines**
* Use **IRSA**, **Karpenter**, or **Fargate** in production
* Apply **Pod Security**, **RBAC**, **Logging**, and **Monitoring**

---

## 🎁 Optional: GitHub Repo + CI/CD Example

Would you like:

✅ GitHub repo with this project scaffold
✅ GitHub Actions workflow for CI/CD
✅ PDF or PowerPoint version of this hands-on
✅ Full demo with Helm charts + ArgoCD?


