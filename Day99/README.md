# Day 99 - Challenge 
# KodeKloud Task: Secure DynamoDB Table + IAM Role/Policy with Terraform

## Task Description

The DevOps team has been tasked with creating a secure DynamoDB table and enforcing fine-grained access control using IAM. This setup will allow secure and restricted access to the table from trusted AWS services only.

As a member of the Nautilus DevOps Team, perform the following using Terraform:

1. **Create a DynamoDB Table:** Create a table named `datacenter-table` with minimal configuration.
2. **Create an IAM Role:** Create an IAM role named `datacenter-role` that will be allowed to access the table.
3. **Create an IAM Policy:** Create a policy named `datacenter-readonly-policy` that grants read-only access (GetItem, Scan, Query) to the specific DynamoDB table and attach it to the role.
4. **Create `main.tf`** (do not create a separate `.tf` file) to provision the table, role, and policy.
5. **Create `variables.tf`** with these variables:
   - `KKE_TABLE_NAME`: name of the DynamoDB table
   - `KKE_ROLE_NAME`: name of the IAM role
   - `KKE_POLICY_NAME`: name of the IAM policy
6. **Create `outputs.tf`** with these outputs:
   - `kke_dynamodb_table`: name of the DynamoDB table
   - `kke_iam_role_name`: name of the IAM role
   - `kke_iam_policy_name`: name of the IAM policy
7. **Define the actual values** for these variables in the `terraform.tfvars` file.
8. Ensure the IAM policy allows **only read access** and is restricted to the specific DynamoDB table created.

**Notes:**

1. The Terraform working directory is `/home/bob/terraform`.
2. Right-click under the `EXPLORER` section in VS Code and select `Open in Integrated Terminal` to launch the terminal.
3. Before submitting, ensure `terraform plan` returns `No changes. Your infrastructure matches the configuration.`

---

## Solution Steps

### Step 0: Go to the working directory

```bash
cd /home/bob/terraform
ls
```

> Check if a `provider` block or region already exists in any file. If the environment already configures the provider (e.g. a custom endpoint), keep that setup and match its region/settings instead of `us-east-1` below.

### Step 1: Create `variables.tf`

```hcl
variable "KKE_TABLE_NAME" {
  type = string
}

variable "KKE_ROLE_NAME" {
  type = string
}

variable "KKE_POLICY_NAME" {
  type = string
}
```

### Step 2: Create `terraform.tfvars`

```hcl
KKE_TABLE_NAME  = "datacenter-table"
KKE_ROLE_NAME   = "datacenter-role"
KKE_POLICY_NAME = "datacenter-readonly-policy"
```

### Step 3: Create `main.tf`

```hcl
provider "aws" {
  region = "us-east-1"
}

# 1. DynamoDB table (minimal config)
resource "aws_dynamodb_table" "datacenter_table" {
  name         = var.KKE_TABLE_NAME
  billing_mode = "PAY_PER_REQUEST"
  hash_key     = "id"

  attribute {
    name = "id"
    type = "S"
  }
}

# 2. IAM role (trusted AWS service can assume it)
resource "aws_iam_role" "datacenter_role" {
  name = var.KKE_ROLE_NAME

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect = "Allow"
        Principal = {
          Service = "ec2.amazonaws.com"
        }
        Action = "sts:AssumeRole"
      }
    ]
  })
}

# 3. IAM policy (read-only, only for this table)
resource "aws_iam_policy" "datacenter_readonly_policy" {
  name = var.KKE_POLICY_NAME

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect = "Allow"
        Action = [
          "dynamodb:GetItem",
          "dynamodb:Scan",
          "dynamodb:Query"
        ]
        Resource = aws_dynamodb_table.datacenter_table.arn
      }
    ]
  })
}

# 4. Attach policy to role
resource "aws_iam_role_policy_attachment" "attach" {
  role       = aws_iam_role.datacenter_role.name
  policy_arn = aws_iam_policy.datacenter_readonly_policy.arn
}
```

### Step 4: Create `outputs.tf`

```hcl
output "kke_dynamodb_table" {
  value = aws_dynamodb_table.datacenter_table.name
}

output "kke_iam_role_name" {
  value = aws_iam_role.datacenter_role.name
}

output "kke_iam_policy_name" {
  value = aws_iam_policy.datacenter_readonly_policy.name
}
```

### Step 5: Run Terraform

```bash
terraform init
terraform validate
terraform apply -auto-approve
```

### Step 6: Verify (required before submitting)

```bash
terraform plan
```

Expected output:

```
No changes. Your infrastructure matches the configuration.
```

---

## Quick Explanation

- **Table:** `PAY_PER_REQUEST` with a single `id` key is the simplest setup.
- **Role:** the `assume_role_policy` defines which AWS service (EC2) can use the role.
- **Policy:** allows only `GetItem`, `Scan`, `Query` (read-only), and `Resource` points to this table's ARN only.
- **Attachment:** links the policy to the role.
- **Variables:** names are declared in `variables.tf`, with real values set in `terraform.tfvars`.
