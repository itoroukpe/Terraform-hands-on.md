Here’s a **step-by-step hands-on Terraform tutorial** to **create a complete, highly available VPC and EKS (Elastic Kubernetes Service) Cluster** using AWS. This is an essential exercise for DevOps students aiming to master Infrastructure as Code and Kubernetes on AWS.

---

# 🚀 Terraform Hands-On: Create a Complete VPC & Highly Available EKS Cluster on AWS

---

## ✅ Overview

You’ll create:

* A custom VPC with public/private subnets in **multiple Availability Zones**
* NAT gateway, route tables, and security groups
* An EKS Cluster with managed node groups spread across multiple AZs

---

## 🧰 Prerequisites

* AWS Account & IAM user with **Admin access**
* AWS CLI configured (`aws configure`)
* Terraform installed (`terraform -v`)
* `kubectl` installed
* `eksctl` optional (for troubleshooting)
* A key pair created in your AWS region

---

## 📁 Step 1: Project Structure

```bash
mkdir terraform-eks-ha
cd terraform-eks-ha

touch main.tf vpc.tf eks.tf outputs.tf variables.tf terraform.tfvars provider.tf
```

---

## 🔧 Step 2: Terraform Files

### ✅ `provider.tf`

```hcl
provider "aws" {
  region = var.region
}
```

---

### ✅ `variables.tf`

```hcl
variable "region" {
  default = "us-west-2"
}

variable "cluster_name" {
  default = "demo-eks-cluster"
}

variable "vpc_cidr" {
  default = "10.0.0.0/16"
}

variable "public_subnets" {
  type    = list(string)
  default = ["10.0.1.0/24", "10.0.2.0/24"]
}

variable "private_subnets" {
  type    = list(string)
  default = ["10.0.3.0/24", "10.0.4.0/24"]
}

variable "azs" {
  type    = list(string)
  default = ["us-west-2a", "us-west-2b"]
}

variable "node_instance_type" {
  default = "t3.medium"
}

variable "desired_capacity" {
  default = 2
}
```

---

### ✅ `vpc.tf`

```hcl
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "5.1.2"

  name = "eks-vpc"
  cidr = var.vpc_cidr

  azs             = var.azs
  public_subnets  = var.public_subnets
  private_subnets = var.private_subnets

  enable_nat_gateway = true
  single_nat_gateway = true

  tags = {
    Name        = "eks-vpc"
    Environment = "dev"
  }
}
```

> This uses the **Terraform AWS VPC module** to provision a multi-AZ setup with public/private subnets and a NAT Gateway.

---

### ✅ `eks.tf`

```hcl
module "eks" {
  source          = "terraform-aws-modules/eks/aws"
  version         = "20.4.0"

  cluster_name    = var.cluster_name
  cluster_version = "1.28"
  subnet_ids      = module.vpc.private_subnets
  vpc_id          = module.vpc.vpc_id
  enable_irsa     = true

  eks_managed_node_groups = {
    default_node_group = {
      desired_size = var.desired_capacity
      max_size     = 3
      min_size     = 1

      instance_types = [var.node_instance_type]
      subnet_ids     = module.vpc.private_subnets

      tags = {
        Name = "eks-node"
      }
    }
  }

  tags = {
    Environment = "dev"
    Terraform   = "true"
  }
}
```

> This uses the **Terraform EKS module** to create a highly available EKS cluster with managed node groups.

---

### ✅ `outputs.tf`

```hcl
output "cluster_name" {
  value = module.eks.cluster_name
}

output "cluster_endpoint" {
  value = module.eks.cluster_endpoint
}

output "cluster_security_group_id" {
  value = module.eks.cluster_security_group_id
}

output "node_group_role_arn" {
  value = module.eks.eks_managed_node_groups["default_node_group"].iam_role_arn
}

output "kubeconfig" {
  value = module.eks.kubeconfig
  sensitive = true
}
```

---

### ✅ `terraform.tfvars`

```hcl
region = "us-west-2"
cluster_name = "demo-eks-cluster"
```

---

## 🛠️ Step 3: Initialize and Apply Terraform

```bash
terraform init
terraform plan
terraform apply
```

Type `yes` when prompted.

---

## 🔗 Step 4: Configure kubectl Access

Once Terraform finishes:

```bash
aws eks --region us-west-2 update-kubeconfig --name demo-eks-cluster
```

Check cluster access:

```bash
kubectl get nodes
```

---

## ✅ Step 5: Validate Cluster

* Run:

  ```bash
  kubectl get svc
  kubectl get pods -A
  ```
* Deploy a test app:

  ```bash
  kubectl create deployment hello-app --image=nginx
  kubectl expose deployment hello-app --type=LoadBalancer --port=80
  kubectl get svc
  ```

Wait a few minutes and access via the ELB DNS name.

---

## 🧹 Step 6: Cleanup

```bash
terraform destroy
```

---

## 🧠 Extra: Best Practices

* Use remote state (S3 + DynamoDB)
* Add IAM policies for Kubernetes service accounts (IRSA)
* Add logging and monitoring (CloudWatch, Prometheus, Grafana)
* Use Helm charts for Kubernetes apps
* Automate via CI/CD pipelines

---

