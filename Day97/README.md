# Day 97 - Challenge 
# KodeKloud Task: Create IAM Policy with Terraform

## Task Description
The Nautilus DevOps team is configuring IAM resources on AWS and has the following requirements:

- Create an IAM policy named `iampolicy_mark` in the `us-east-1` region using Terraform.
- The policy must allow read-only access to the EC2 console, i.e. users must be able to view all instances, AMIs, and snapshots in the Amazon EC2 console.
- The Terraform working directory is `/home/bob/terraform`.
- Create the `main.tf` file (do not create a different `.tf` file) to accomplish this task.
- Note: Right-click under the `EXPLORER` section in VS Code and select `Open in Integrated Terminal` to launch the terminal.

---

## Solution Steps

### Step 1: Open the terminal
In VS Code, right-click in the **EXPLORER** section and choose **Open in Integrated Terminal**.

### Step 2: Go to the working directory
```bash
cd /home/bob/terraform
```

### Step 3: Check the existing provider file
A `provider.tf` file already exists and contains the AWS provider, so do NOT add a provider block in `main.tf`.
```bash
cat provider.tf
```

### Step 4: Create / edit `main.tf`
```bash
vi main.tf
```
If the file already has content, delete it first (press `Esc`, type `ggdG`). Then press `i` to enter insert mode and paste:

```hcl
resource "aws_iam_policy" "iampolicy_mark" {
  name        = "iampolicy_mark"
  description = "Read-only access to EC2 console"

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect = "Allow"
        Action = [
          "ec2:Describe*",
          "ec2:GetConsoleOutput",
          "ec2:GetConsoleScreenshot"
        ]
        Resource = "*"
      }
    ]
  })
}
```

Save and exit: press `Esc`, type `:wq`, press `Enter`.

### Step 5: Initialize Terraform
```bash
terraform init
```

### Step 6: Check the plan
```bash
terraform plan
```
You should see **1 resource to add**.

### Step 7: Apply the configuration
```bash
terraform apply
```
Type `yes` when asked (or use `terraform apply -auto-approve`).

### Step 8: Verify
```bash
terraform state list
```
Expected output:
```
aws_iam_policy.iampolicy_mark
```

---

## Explanation
| Part | Meaning |
|------|---------|
| `aws_iam_policy` | Resource that creates the IAM policy |
| `name = "iampolicy_mark"` | Must match the required policy name exactly |
| `ec2:Describe*` | Allows viewing instances, AMIs, snapshots, and more |
| `ec2:GetConsoleOutput` / `ec2:GetConsoleScreenshot` | Read-only console access to instances |
| `Resource = "*"` | Applies to all EC2 resources |

## Troubleshooting
- **Duplicate provider configuration error**: Remove the `provider "aws"` block from `main.tf`. It already exists in `provider.tf`.
- **Other errors**: Read the error message carefully. It is usually a typo in `main.tf`.
- Only use `main.tf` and do not create any other `.tf` files.