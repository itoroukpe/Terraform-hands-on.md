 **step-by-step guide** on how to **store your Terraform state on Terraform Cloud**, which is a best practice for team collaboration, state locking, and history tracking.

---

# ☁️ Step-by-Step: Store Terraform State on Terraform Cloud

---

## ✅ Why Use Terraform Cloud for State?

* Centralized remote backend
* Automatic state locking
* Version history of your state
* Secure state storage
* Collaboration with multiple users

---

## 🛠 Prerequisites

* A [Terraform Cloud](https://app.terraform.io/) account
* A Terraform **organization** created in Terraform Cloud
* A **workspace** in that organization
* Terraform CLI installed (v1.1+)

---

## 🧭 Step 1: Login to Terraform Cloud from CLI

```bash
terraform login
```

* Opens a browser window to generate an API token
* Stores the token in `~/.terraform.d/credentials.tfrc.json`

---

## 🏗️ Step 2: Create a Workspace in Terraform Cloud

1. Go to [Terraform Cloud](https://app.terraform.io/)
2. Select your **organization**
3. Click **"New Workspace"**
4. Choose:

   * **CLI-driven workflow**
   * Name it something like `myproject-dev` or `vpc-aws-workspace`

---

## 📄 Step 3: Configure Terraform Backend in Your Code

In your project folder, create or update `backend.tf`:

```hcl
terraform {
  backend "remote" {
    hostname     = "app.terraform.io"
    organization = "your-org-name"

    workspaces {
      name = "your-workspace-name"
    }
  }
}
```

Replace:

* `"your-org-name"` with your actual Terraform Cloud org
* `"your-workspace-name"` with the name of your workspace

> ✅ This tells Terraform to use Terraform Cloud to store the state.

---

## ⚙️ Step 4: Initialize the Backend

```bash
terraform init
```

You'll see output like:

```
Terraform has been successfully initialized!
Terraform has detected you're using a remote backend hosted on Terraform Cloud.
```

> It will ask to migrate state (if one already exists).

---

## 🚀 Step 5: Run Terraform Commands

Use Terraform like normal:

```bash
terraform plan
terraform apply
```

The state file is now managed in Terraform Cloud under the configured workspace.

---

## 🔎 Step 6: Verify in Terraform Cloud

* Go to your **workspace** in Terraform Cloud
* Click **"States"** tab to see:

  * Stored `.tfstate`
  * History of changes
  * Outputs and resources

---

## 🧼 Optional: Use `terraform.tfvars` or Environment Variables

You can define environment-specific variables in:

* `terraform.tfvars`
* Terraform Cloud workspace variables tab
* CLI `-var` arguments

---

## ✅ Summary

| Step | Action                                       |
| ---- | -------------------------------------------- |
| 1    | Login with `terraform login`                 |
| 2    | Create a Terraform Cloud workspace           |
| 3    | Add `backend "remote"` block in `backend.tf` |
| 4    | Run `terraform init` to connect              |
| 5    | Use `plan` and `apply` as usual              |
| 6    | Verify in Terraform Cloud web UI             |

---


