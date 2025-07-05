 **step-by-step hands-on guide using Terraform** to create a **complete AWS VPC with 2 EC2 instances**. 
This is an essential foundational exercise for DevOps students to learn real-world Infrastructure as Code (IaC) practices.

---

# 🚀 Terraform Tutorial: Create a Complete VPC with 2 EC2 Instances

## 🧰 Prerequisites

✅ AWS Account
✅ AWS CLI Configured (`aws configure`)
✅ Terraform Installed (`terraform -v`)
✅ Text editor (e.g., VS Code)

---

## 📁 Step 1: Create Project Directory

```bash
mkdir terraform-vpc-ec2-demo
cd terraform-vpc-ec2-demo
```

---

## 📄 Step 2: Create Terraform Configuration Files

Create the following files:

```bash
touch provider.tf vpc.tf ec2.tf variables.tf outputs.tf terraform.tfvars
```

---

### ✅ 1. `provider.tf`

```hcl
provider "aws" {
  region = var.aws_region
}
```

---

### ✅ 2. `variables.tf`

```hcl
variable "aws_region" {
  default = "us-east-1"
}

variable "vpc_cidr" {
  default = "10.0.0.0/16"
}

variable "subnet_cidr" {
  default = "10.0.1.0/24"
}

variable "availability_zone" {
  default = "us-east-1a"
}

variable "instance_type" {
  default = "t2.micro"
}

variable "ami_id" {
  description = "Amazon Linux 2 AMI ID"
  default     = "ami-0c02fb55956c7d316"
}
```

---

### ✅ 3. `vpc.tf`

```hcl
# Create VPC
resource "aws_vpc" "main_vpc" {
  cidr_block = var.vpc_cidr
  tags = {
    Name = "MainVPC"
  }
}

# Create Subnet
resource "aws_subnet" "main_subnet" {
  vpc_id                  = aws_vpc.main_vpc.id
  cidr_block              = var.subnet_cidr
  availability_zone       = var.availability_zone
  map_public_ip_on_launch = true

  tags = {
    Name = "MainSubnet"
  }
}

# Internet Gateway
resource "aws_internet_gateway" "gw" {
  vpc_id = aws_vpc.main_vpc.id

  tags = {
    Name = "MainIGW"
  }
}

# Route Table
resource "aws_route_table" "rt" {
  vpc_id = aws_vpc.main_vpc.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.gw.id
  }

  tags = {
    Name = "MainRouteTable"
  }
}

# Route Table Association
resource "aws_route_table_association" "a" {
  subnet_id      = aws_subnet.main_subnet.id
  route_table_id = aws_route_table.rt.id
}
```

---

### ✅ 4. `ec2.tf`

```hcl
# Security Group
resource "aws_security_group" "ec2_sg" {
  name        = "allow_ssh"
  description = "Allow SSH inbound traffic"
  vpc_id      = aws_vpc.main_vpc.id

  ingress {
    description = "SSH"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "EC2SecurityGroup"
  }
}

# Create 2 EC2 instances
resource "aws_instance" "ec2_instance" {
  count         = 2
  ami           = var.ami_id
  instance_type = var.instance_type
  subnet_id     = aws_subnet.main_subnet.id
  security_groups = [aws_security_group.ec2_sg.name]

  tags = {
    Name = "TerraformInstance-${count.index + 1}"
  }
}
```

---

### ✅ 5. `outputs.tf`

```hcl
output "instance_public_ips" {
  value = aws_instance.ec2_instance[*].public_ip
}
```

---

### ✅ 6. `terraform.tfvars` (Optional)

```hcl
aws_region       = "us-east-1"
vpc_cidr         = "10.0.0.0/16"
subnet_cidr      = "10.0.1.0/24"
availability_zone = "us-east-1a"
instance_type    = "t2.micro"
ami_id           = "ami-0c02fb55956c7d316"
```

---

## 🛠️ Step 3: Initialize Terraform

```bash
terraform init
```

---

## 🔍 Step 4: Validate & Plan

```bash
terraform validate
terraform plan
```

---

## 🚀 Step 5: Apply Configuration

```bash
terraform apply
```

Type `yes` when prompted.

> 🖥️ After apply, you'll get public IPs for the 2 EC2 instances.

---

## ✅ Step 6: Verify in AWS Console

* Go to **EC2** → confirm 2 instances running
* Go to **VPC** → check custom VPC, subnet, IGW, and route table
* Try connecting using SSH:

```bash
ssh ec2-user@<public_ip> -i /path/to/your/key.pem
```

---

## 🧹 Step 7: Clean Up

```bash
terraform destroy
```

---

## 🧠 What's Next?

* Add multiple subnets (for HA)
* Split into reusable modules
* Use remote backend for state
* Add Load Balancer (ALB) and Route53
* Automate with CI/CD (GitHub Actions)

---

