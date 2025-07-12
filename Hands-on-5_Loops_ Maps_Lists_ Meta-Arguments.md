# 🎯 Terraform Hands-On: Loops, Maps, Lists & Meta-Arguments (Best Practice File Structure)

* `count` and `for_each`
* `for` loops with maps
* `toset`, `tomap`
* `for` with `if` filters

---

## 🗂️ File Structure

```
terraform-loop-lab/
├── main.tf
├── variables.tf
└── outputs.tf
```

---

## ✅ Step 1: Set Up the Project

```bash
mkdir terraform-loop-lab
cd terraform-loop-lab
touch main.tf variables.tf outputs.tf
```

---

## ✍️ 1. `variables.tf` — Input Declarations

```hcl
# List for EC2 instances using count
variable "instance_names" {
  default = ["dev-server", "qa-server", "prod-server"]
}

# Map for EC2 instances using for_each
variable "envs" {
  default = {
    dev     = "10.0.1.10"
    qa      = "10.0.1.11"
    staging = "10.0.1.12"
  }
}

# List with duplicates (for toset)
variable "zones" {
  default = ["us-west-2a", "us-west-2b", "us-west-2a"]
}

# Map for for-loop transformation
variable "teams" {
  default = {
    backend  = "blue"
    frontend = "green"
  }
}

# Map for filtering with for + if
variable "users" {
  default = {
    alice  = "admin"
    bob    = "viewer"
    clara  = "admin"
    daniel = "viewer"
  }
}
```

---

## ⚙️ 2. `main.tf` — Resources & Logic

```hcl
provider "aws" {
  region = "us-west-2"
}

# EC2 using count
resource "aws_instance" "web" {
  count         = length(var.instance_names)
  ami           = "ami-0c02fb55956c7d316"
  instance_type = "t2.micro"
  tags = {
    Name = var.instance_names[count.index]
  }
}

# EC2 using for_each
resource "aws_instance" "env_server" {
  for_each      = var.envs
  ami           = "ami-0c02fb55956c7d316"
  instance_type = "t2.micro"
  tags = {
    Name = "server-${each.key}"
    IP   = each.value
  }
}

# Local value using tomap
locals {
  raw_envs = [
    { name = "dev", cidr = "10.0.1.0/24" },
    { name = "qa",  cidr = "10.0.2.0/24" }
  ]

  env_map = tomap({ for env in local.raw_envs : env.name => env.cidr })
}
```

---

## 📤 3. `outputs.tf` — Outputs & Loop Results

```hcl
# Deduplicate zones
output "unique_zones" {
  value = toset(var.zones)
}

# Convert list of maps to map
output "env_map" {
  value = local.env_map
}

# Loop transformation with map
output "team_tags" {
  value = {
    for k, v in var.teams : k => "team-${v}"
  }
}

# Filter with if
output "admin_users" {
  value = [for user, role in var.users : user if role == "admin"]
}

# EC2 instance names
output "created_instances" {
  value = aws_instance.web[*].tags.Name
}

# EC2 instances from envs map
output "env_instances" {
  value = [for name in keys(var.envs) : "server-${name}"]
}
```

---

## 🧪 Step-by-Step Run

1. **Initialize Terraform**

```bash
terraform init
```

2. **Validate the Configuration**

```bash
terraform validate
```

3. **Preview the Plan**

```bash
terraform plan
```

4. **Apply the Configuration**

```bash
terraform apply
```

Type `yes` to confirm.

---

## ✅ What You'll Learn

| Terraform Concept | Demonstrated In             |
| ----------------- | --------------------------- |
| `count`           | Create EC2s from a list     |
| `for_each`        | Create EC2s from a map      |
| `toset()`         | Deduplicate AZs             |
| `tomap()`         | Convert list-of-maps to map |
| `for + map`       | Custom tag generation       |
| `for + if`        | Conditional filtering       |

---

## 🧼 Cleanup

```bash
terraform destroy
```

---


