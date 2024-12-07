# Terraform-hands-on.md
### **Hands-On Workshop: Terraform with AWS**

This workshop is designed to provide participants with a practical, step-by-step approach to mastering Terraform for managing AWS infrastructure. Participants will install Terraform, create and manage AWS resources, and learn advanced concepts like input variables, output queries, remote state storage, and modules.

---

### **Prerequisites:**
1. **Software Requirements:**
   - Terraform installed on your local machine.
   - AWS CLI installed and configured with credentials.
   - A text editor (e.g., VSCode, Sublime Text, or Notepad++).
   - Basic knowledge of AWS services.
2. **AWS Account:**
   - Ensure you have an active AWS account with appropriate IAM permissions for creating resources.

---

### **Workshop Agenda**

---

### **Part 1: Installing Terraform**
1. **Download Terraform:**
   - Visit [Terraform Downloads](https://www.terraform.io/downloads.html).
   - Select the appropriate package for your operating system.

2. **Install Terraform:**
   - Extract the downloaded archive and place the Terraform binary in a directory included in your system’s `PATH`.
   - Confirm installation:
     ```bash
     terraform version
     ```
     You should see the installed version.

---

### **Part 2: Build Infrastructure**
**Objective:** Create an AWS EC2 instance using Terraform.

1. **Initialize Terraform:**
   - Create a new directory:
     ```bash
     mkdir terraform-aws-ec2
     cd terraform-aws-ec2
     ```
   - Write a `main.tf` file:
     ```hcl
     provider "aws" {
       region = "us-west-2"
     }

     resource "aws_instance" "web" {
       ami           = "ami-0c55b159cbfafe1f0"
       instance_type = "t2.micro"

       tags = {
         Name = "TerraformWorkshop"
       }
     }
     ```
   - Initialize Terraform:
     ```bash
     terraform init
     ```

2. **Plan and Apply Configuration:**
   - Preview changes:
     ```bash
     terraform plan
     ```
   - Apply the configuration:
     ```bash
     terraform apply
     ```
   - Confirm the instance is created by checking the AWS Management Console.

---

### **Part 3: Change Infrastructure**
**Objective:** Modify the EC2 instance to include a tag.

1. **Update `main.tf`:**
   Add a new tag:
   ```hcl
   tags = {
     Name    = "TerraformWorkshop"
     Project = "Terraform Hands-On"
   }
   ```

2. **Apply Changes:**
   - Run the commands:
     ```bash
     terraform plan
     terraform apply
     ```
   - Verify the changes in the AWS Management Console.

---

### **Part 4: Destroy Infrastructure**
**Objective:** Remove all resources created by Terraform.

1. **Run the Destroy Command:**
   ```bash
   terraform destroy
   ```
2. **Confirm Resource Deletion:**
   Check the AWS Management Console to ensure the instance is deleted.

---

### **Part 5: Define Input Variables**
**Objective:** Use variables to parameterize the instance type and region.

1. **Create `variables.tf`:**
   ```hcl
   variable "region" {
     default = "us-west-2"
   }

   variable "instance_type" {
     default = "t2.micro"
   }
   ```

2. **Update `main.tf`:**
   ```hcl
   provider "aws" {
     region = var.region
   }

   resource "aws_instance" "web" {
     ami           = "ami-0c55b159cbfafe1f0"
     instance_type = var.instance_type

     tags = {
       Name = "TerraformWorkshop"
     }
   }
   ```

3. **Apply Configuration:**
   ```bash
   terraform apply
   ```

---

### **Part 6: Query Data with Outputs**
**Objective:** Display the public IP of the created EC2 instance.

1. **Add Outputs to `main.tf`:**
   ```hcl
   output "instance_ip" {
     value = aws_instance.web.public_ip
   }
   ```

2. **Apply Configuration and View Outputs:**
   ```bash
   terraform apply
   terraform output
   ```

---

### **Part 7: Store Remote State**
**Objective:** Save Terraform state remotely using S3.

1. **Configure S3 Backend:**
   Update `main.tf`:
   ```hcl
   terraform {
     backend "s3" {
       bucket         = "my-terraform-state"
       key            = "ec2/terraform.tfstate"
       region         = "us-west-2"
     }
   }
   ```

2. **Initialize Backend:**
   ```bash
   terraform init
   ```

3. **Apply Configuration:**
   ```bash
   terraform apply
   ```

---

### **Part 8: All Terraform Blocks**
1. **Provider Block:** Defines the AWS provider and region.
   ```hcl
   provider "aws" {
     region = "us-west-2"
   }
   ```

2. **Resource Block:** Declares the resources to be created.
   ```hcl
   resource "aws_instance" "web" {
     ...
   }
   ```

3. **Variable Block:** Parameterizes configurations.
   ```hcl
   variable "region" {
     default = "us-west-2"
   }
   ```

4. **Output Block:** Provides outputs after applying configurations.
   ```hcl
   output "instance_ip" {
     value = aws_instance.web.public_ip
   }
   ```

5. **Data Block:** Retrieves information about existing AWS resources.
   ```hcl
   data "aws_ami" "latest" {
     ...
   }
   ```

6. **Module Block:** Calls external or custom modules.

---

### **Part 9: Terraform Modules**
**Objective:** Create a reusable module for EC2 instances.

1. **Create a Module Directory:**
   ```bash
   mkdir -p modules/ec2
   cd modules/ec2
   ```

2. **Define Module Files:**
   - `main.tf`:
     ```hcl
     resource "aws_instance" "web" {
       ami           = var.ami
       instance_type = var.instance_type

       tags = {
         Name = var.instance_name
       }
     }
     ```

   - `variables.tf`:
     ```hcl
     variable "ami" {}
     variable "instance_type" {}
     variable "instance_name" {}
     ```

   - `outputs.tf`:
     ```hcl
     output "instance_id" {
       value = aws_instance.web.id
     }
     ```

3. **Use the Module in `main.tf`:**
   ```hcl
   module "ec2" {
     source         = "./modules/ec2"
     ami            = "ami-0c55b159cbfafe1f0"
     instance_type  = "t2.micro"
     instance_name  = "TerraformWorkshop"
   }
   ```

4. **Initialize and Apply:**
   ```bash
   terraform init
   terraform apply
   ```

---

### **Wrap-Up**
By the end of this workshop, participants will:
1. Understand Terraform basics and core blocks.
2. Create, update, and destroy AWS resources using Terraform.
3. Work with input variables, outputs, and remote states.
4. Develop reusable Terraform modules for scalable infrastructure.  

Encourage participants to explore advanced topics like conditionals, loops, and working with multiple environments to build on this foundational knowledge.
