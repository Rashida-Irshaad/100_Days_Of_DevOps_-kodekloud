# Day 95 - Challenge 
# KodeKloud Task: Create a Security Group with Terraform

## Task Description
The Nautilus DevOps team is migrating part of their infrastructure to AWS in small, incremental steps.

Use Terraform to create a security group under the **default VPC** with these requirements:

1. Name of the security group: `devops-sg`
2. Description: `Security group for Nautilus App Servers`
3. Inbound rule: type `HTTP`, port `80`, source CIDR `0.0.0.0/0`
4. Inbound rule: type `SSH`, port `22`, source CIDR `0.0.0.0/0`

Other requirements:
- Region must be `us-east-1`
- Terraform working directory: `/home/bob/terraform`
- Create the `main.tf` file only (do not create a different `.tf` file)

---

## Solution Steps

### Step 1: Open the terminal
In VS Code, right-click under **EXPLORER** → **Open in Integrated Terminal**.

### Step 2: Go to the working directory
```bash
cd /home/bob/terraform
```

### Step 3: Check the existing provider file
The directory already contains `provider.tf` with the AWS provider. Do NOT add another provider block in `main.tf`, or you will get a "Duplicate provider configuration" error.
```bash
cat provider.tf
```
Confirm the region is `us-east-1`.

### Step 4: Create/open `main.tf`
```bash
vi main.tf
```
Press `i` to enter insert mode.

### Step 5: Paste this code (no provider block)
```hcl
data "aws_vpc" "default" {
  default = true
}

resource "aws_security_group" "devops_sg" {
  name        = "devops-sg"
  description = "Security group for Nautilus App Servers"
  vpc_id      = data.aws_vpc.default.id

  ingress {
    description = "HTTP"
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    description = "SSH"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```
Save and exit: press `Esc`, type `:wq`, press `Enter`.

### Step 6: Initialize Terraform
```bash
terraform init
```

### Step 7: Check the plan
```bash
terraform plan
```
You should see **1 resource to add**.

### Step 8: Apply
```bash
terraform apply -auto-approve
```

### Step 9: Verify (optional)
```bash
terraform show
```
Or in the AWS console: **EC2 → Security Groups → devops-sg**.

---

## Code Explanation

| Part | Meaning |
|---|---|
| `data "aws_vpc" "default"` | Finds the default VPC automatically |
| `name` / `description` | Exactly as the task requires |
| First `ingress` | Allows HTTP (port 80) from anywhere |
| Second `ingress` | Allows SSH (port 22) from anywhere |
| `vpc_id` | Places the security group in the default VPC |

## Common Issues
- **Duplicate provider configuration:** remove any `provider "aws"` block from `main.tf` (it already exists in `provider.tf`).
- **Do not create other `.tf` files** such as `variables.tf`.
- **Credentials errors:** KodeKloud preconfigures them, so no keys are needed.
- **Wrong region:** if `provider.tf` shows a region other than `us-east-1`, tell your instructor/check the task, since the task requires `us-east-1`.