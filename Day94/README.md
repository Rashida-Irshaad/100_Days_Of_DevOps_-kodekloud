# Day 94 - Challenge 
# KodeKloud Task: Create a VPC with Terraform

## Task Description
The Nautilus DevOps team is migrating part of their infrastructure to AWS in small, incremental steps.

**Requirements:**
- Create a VPC named `xfusion-vpc` in region `us-east-1` with any IPv4 CIDR block using Terraform.
- Terraform working directory: `/home/bob/terraform`
- Create the `main.tf` file only (do not create a different `.tf` file).
- Note: Right-click under the **EXPLORER** section in VS Code and select **Open in Integrated Terminal**.

---

## Solution Steps

### Step 1: Open the terminal
Right-click on the empty space under **EXPLORER** in VS Code and choose **Open in Integrated Terminal**.

### Step 2: Go to the working directory
```bash
cd /home/bob/terraform
```

### Step 3: Check the existing provider file
```bash
cat provider.tf
```
The lab already has the AWS provider configured here (region `us-east-1`), so do **not** add another `provider` block in `main.tf`. Doing so causes a "Duplicate provider configuration" error.

### Step 4: Open `main.tf`
```bash
vi main.tf
```

### Step 5: Clear any old content
Press `Esc`, then type:
```
gg
dG
```

### Step 6: Add the VPC code
Press `i` (insert mode) and paste:

```hcl
resource "aws_vpc" "xfusion_vpc" {
  cidr_block = "10.0.0.0/16"

  tags = {
    Name = "xfusion-vpc"
  }
}
```

### Step 7: Save and exit
Press `Esc`, type `:wq`, press Enter.

### Step 8: Verify the file
```bash
cat main.tf
```
It should contain only the `aws_vpc` resource block.

### Step 9: Initialize Terraform
```bash
terraform init
```

### Step 10: Apply the configuration
```bash
terraform apply
```
Type `yes` when asked to confirm.

### Step 11: Check the result
Expected output:
```
Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

Optional verification:
```bash
terraform state list
```
You should see `aws_vpc.xfusion_vpc`.

---

## Common Issues
- **Duplicate provider configuration:** Remove any `provider "aws"` block from `main.tf`, since `provider.tf` already has it.
- **Name not showing:** Make sure the tag key is `Name` with a capital N.
- **Wrong file:** Only `main.tf` should be edited; don't create other `.tf` files.
