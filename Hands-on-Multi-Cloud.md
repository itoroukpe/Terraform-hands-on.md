 **step-by-step, hands-on Terraform guide** to create a **complete VPC and EC2/VM infrastructure** on **multi-cloud (AWS, GCP, Azure)**, with support for **multiple environments** (`dev`, `qa`, `staging`, `prod`).

---

# 🌐 Multi-Cloud VPC + Compute with Terraform

## Supports: **AWS (EC2)** | **GCP (Compute Engine)** | **Azure (Virtual Machine)**

## Environments: `dev`, `qa`, `staging`, `prod`

---

## 📁 Project Structure

```
multi-cloud-terraform/
├── modules/
│   ├── aws/
│   │   └── vpc_ec2.tf
│   ├── gcp/
│   │   └── network_vm.tf
│   └── azure/
│       └── vnet_vm.tf
├── envs/
│   ├── dev/
│   ├── qa/
│   ├── staging/
│   └── prod/
│       └── main.tf
├── variables.tf
├── terraform.tfvars
└── outputs.tf
```

---

## 🔧 Step 1: Define Reusable Modules

---

### 📌 modules/aws/vpc\_ec2.tf

```hcl
provider "aws" {
  region = var.aws_region
}

resource "aws_vpc" "main" {
  cidr_block = var.vpc_cidr
  tags = { Name = "${var.env}-vpc" }
}

resource "aws_subnet" "subnet" {
  vpc_id            = aws_vpc.main.id
  cidr_block        = var.subnet_cidr
  availability_zone = var.aws_az
  tags = { Name = "${var.env}-subnet" }
}

resource "aws_instance" "vm" {
  ami           = var.aws_ami
  instance_type = var.instance_type
  subnet_id     = aws_subnet.subnet.id
  key_name      = var.key_name
  tags = { Name = "${var.env}-ec2" }
}
```

---

### 📌 modules/gcp/network\_vm.tf

```hcl
provider "google" {
  project = var.gcp_project
  region  = var.gcp_region
}

resource "google_compute_network" "vpc" {
  name                    = "${var.env}-vpc"
  auto_create_subnetworks = false
}

resource "google_compute_subnetwork" "subnet" {
  name          = "${var.env}-subnet"
  ip_cidr_range = var.subnet_cidr
  network       = google_compute_network.vpc.id
  region        = var.gcp_region
}

resource "google_compute_instance" "vm" {
  name         = "${var.env}-gce"
  machine_type = var.instance_type
  zone         = var.gcp_zone

  boot_disk {
    initialize_params {
      image = var.gcp_image
    }
  }

  network_interface {
    subnetwork = google_compute_subnetwork.subnet.name
    access_config {}
  }
}
```

---

### 📌 modules/azure/vnet\_vm.tf

```hcl
provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "rg" {
  name     = "${var.env}-rg"
  location = var.azure_region
}

resource "azurerm_virtual_network" "vnet" {
  name                = "${var.env}-vnet"
  address_space       = [var.vpc_cidr]
  location            = var.azure_region
  resource_group_name = azurerm_resource_group.rg.name
}

resource "azurerm_subnet" "subnet" {
  name                 = "${var.env}-subnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = [var.subnet_cidr]
}

resource "azurerm_network_interface" "nic" {
  name                = "${var.env}-nic"
  location            = var.azure_region
  resource_group_name = azurerm_resource_group.rg.name

  ip_configuration {
    name                          = "internal"
    subnet_id                     = azurerm_subnet.subnet.id
    private_ip_address_allocation = "Dynamic"
  }
}

resource "azurerm_linux_virtual_machine" "vm" {
  name                = "${var.env}-vm"
  resource_group_name = azurerm_resource_group.rg.name
  location            = var.azure_region
  size                = var.instance_type
  admin_username      = "azureuser"
  network_interface_ids = [azurerm_network_interface.nic.id]

  admin_ssh_key {
    username   = "azureuser"
    public_key = file(var.ssh_public_key)
  }

  os_disk {
    caching              = "ReadWrite"
    storage_account_type = "Standard_LRS"
  }

  source_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "18.04-LTS"
    version   = "latest"
  }
}
```

---

## 🔁 Step 2: Shared `variables.tf` and `terraform.tfvars`

```hcl
# variables.tf

variable "env" {}
variable "instance_type" {}

# AWS
variable "aws_region" {}
variable "aws_az" {}
variable "aws_ami" {}
variable "vpc_cidr" {}
variable "subnet_cidr" {}
variable "key_name" {}

# GCP
variable "gcp_project" {}
variable "gcp_region" {}
variable "gcp_zone" {}
variable "gcp_image" {}

# Azure
variable "azure_region" {}
variable "ssh_public_key" {}
```

```hcl
# terraform.tfvars

env             = "dev"
instance_type   = "t2.micro"

aws_region      = "us-east-1"
aws_az          = "us-east-1a"
aws_ami         = "ami-0c02fb55956c7d316"
vpc_cidr        = "10.0.0.0/16"
subnet_cidr     = "10.0.1.0/24"
key_name        = "my-keypair"

gcp_project     = "my-gcp-project"
gcp_region      = "us-central1"
gcp_zone        = "us-central1-a"
gcp_image       = "debian-cloud/debian-11"

azure_region    = "eastus"
ssh_public_key  = "~/.ssh/id_rsa.pub"
```

---

## 🏗️ Step 3: Environment-Specific `main.tf` (e.g., `envs/dev/main.tf`)

```hcl
module "aws" {
  source         = "../../modules/aws"
  env            = var.env
  aws_region     = var.aws_region
  aws_az         = var.aws_az
  aws_ami        = var.aws_ami
  vpc_cidr       = var.vpc_cidr
  subnet_cidr    = var.subnet_cidr
  key_name       = var.key_name
  instance_type  = var.instance_type
}

module "gcp" {
  source         = "../../modules/gcp"
  env            = var.env
  gcp_project    = var.gcp_project
  gcp_region     = var.gcp_region
  gcp_zone       = var.gcp_zone
  gcp_image      = var.gcp_image
  subnet_cidr    = var.subnet_cidr
  instance_type  = var.instance_type
}

module "azure" {
  source         = "../../modules/azure"
  env            = var.env
  azure_region   = var.azure_region
  vpc_cidr       = var.vpc_cidr
  subnet_cidr    = var.subnet_cidr
  instance_type  = var.instance_type
  ssh_public_key = var.ssh_public_key
}
```

---

## 🚀 Step 4: Deploy a Specific Environment

```bash
cd envs/dev
terraform init
terraform plan -var-file=../../terraform.tfvars
terraform apply -var-file=../../terraform.tfvars
```

To deploy `qa`, `staging`, or `prod`, just repeat with a different environment directory and tfvars file.

---

## 🧼 Step 5: Clean Up

```bash
terraform destroy -var-file=../../terraform.tfvars
```

---

## ✅ What You Get

| Environment | AWS (EC2) | GCP (Compute Engine) | Azure (VM) |
| ----------- | --------- | -------------------- | ---------- |
| Dev         | ✅         | ✅                    | ✅          |
| QA          | ✅         | ✅                    | ✅          |
| Staging     | ✅         | ✅                    | ✅          |
| Production  | ✅         | ✅                    | ✅          |

---


