 **step-by-step guide** to set up a **Jenkins CI/CD pipeline** to **automate Terraform deployments** using a **GitOps-style workflow**.

---

# 🚀 Step-by-Step Guide: Automate Terraform Deployments with Jenkins

---

## ✅ Overview

You will:

* Set up a Jenkins pipeline
* Connect it to a Git repo with Terraform code
* Configure environment variables for AWS
* Run `terraform init`, `plan`, and `apply` automatically
* Use S3 + DynamoDB for state & locking (optional but recommended)

---

## 🧰 Prerequisites

* Jenkins installed (locally or on EC2)
* GitHub or GitLab repo with Terraform code (e.g., VPC, EKS)
* AWS credentials with IAM permissions
* S3 bucket and DynamoDB table for remote state (optional)
* Terraform installed on Jenkins agent/master

---

## 📁 Example Repo Structure

```
terraform-jenkins-pipeline/
├── Jenkinsfile
├── main.tf
├── variables.tf
├── outputs.tf
├── terraform.tfvars
└── backend.tf
```

---

## 🔧 Step 1: Install Jenkins & Plugins

1. Launch Jenkins ([http://localhost:8080](http://localhost:8080) or EC2 IP)
2. Install the following plugins:

   * **Git Plugin**
   * **Pipeline Plugin**
   * **AWS Credentials Plugin**
   * **AnsiColor Plugin**

---

## 🔧 Step 2: Install Terraform on Jenkins

SSH into your Jenkins server:

```bash
wget https://releases.hashicorp.com/terraform/1.6.6/terraform_1.6.6_linux_amd64.zip
unzip terraform_1.6.6_linux_amd64.zip
sudo mv terraform /usr/local/bin/
terraform -v
```

---

## 🔐 Step 3: Add AWS Credentials to Jenkins

1. Go to **Jenkins Dashboard → Manage Jenkins → Credentials**
2. Choose `(global)` domain → Add Credentials
3. Kind: **AWS Credentials**
4. Add:

   * **Access Key ID**
   * **Secret Access Key**
   * ID: `aws-terraform-creds`

---

## 📝 Step 4: Create the Jenkinsfile

### ✅ `Jenkinsfile`

```groovy
pipeline {
  agent any
  environment {
    AWS_REGION = 'us-west-2'
    TF_IN_AUTOMATION = 'true'
  }
  options {
    ansiColor('xterm')
  }
  stages {
    stage('Checkout Code') {
      steps {
        git branch: 'main', url: 'https://github.com/your-username/terraform-repo.git'
      }
    }
    stage('Setup Terraform') {
      steps {
        sh 'terraform init'
      }
    }
    stage('Terraform Plan') {
      steps {
        sh 'terraform plan -out=tfplan'
      }
    }
    stage('Terraform Apply') {
      steps {
        input message: 'Approve deployment?'
        sh 'terraform apply tfplan'
      }
    }
  }
  post {
    failure {
      echo 'Build failed!'
    }
    success {
      echo 'Infrastructure deployed successfully!'
    }
  }
}
```

---

## 🏗️ Step 5: Create a Jenkins Pipeline Job

1. **New Item** → Name it `terraform-pipeline`
2. Choose **Pipeline**
3. In **Pipeline script from SCM**:

   * SCM: Git
   * Repo URL: `https://github.com/your-username/terraform-repo.git`
   * Branch: `main`
   * Script Path: `Jenkinsfile`

---

## 🔄 Step 6: Trigger the Pipeline

* Commit and push your Terraform code
* Go to Jenkins → Select your job → Click **Build Now**
* Review the logs to see:

  * Checkout
  * Terraform init
  * Terraform plan
  * Approval prompt
  * Terraform apply

---

## 🛡️ Optional: Use Remote State

Use the following in `backend.tf`:

```hcl
terraform {
  backend "s3" {
    bucket         = "my-terraform-state"
    key            = "prod/terraform.tfstate"
    region         = "us-west-2"
    dynamodb_table = "terraform-locks"
    encrypt        = true
  }
}
```

---

## 🧠 Best Practices

| Area          | Best Practice                                     |
| ------------- | ------------------------------------------------- |
| Credentials   | Use IAM Roles or Jenkins credentials securely     |
| Approvals     | Add `input {}` step for manual apply confirmation |
| State         | Use **S3 + DynamoDB** for team safety             |
| Linting       | Add `tflint`, `checkov`, or `terraform validate`  |
| Notifications | Integrate Slack or email for status updates       |

---

## ✅ Summary

* Jenkins pulled Terraform code from Git
* Ran `init`, `plan`, `apply` in order
* Waited for human approval before making changes
* Ensured secure AWS access using Jenkins credentials

---

## 🎁 Want More?

I can provide:

* ✅ GitHub repo template with Terraform code
* ✅ Jenkins Docker setup for local testing
* ✅ CI/CD with GitHub Actions or GitLab instead
* ✅ Slack notifications integration


