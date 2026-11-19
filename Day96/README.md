# Day 96 - Challenge 

# KodeKloud Task: Create an EC2 Instance Using Terraform

## Task Description

The Nautilus DevOps team is strategizing the migration of a portion of their infrastructure to the AWS cloud. Recognizing the scale of this undertaking, they have opted to approach the migration in incremental steps rather than as a single massive transition. To achieve this, they have segmented large tasks into smaller, more manageable units.

For this task, create an EC2 instance using `Terraform` with the following requirements:

1. The EC2 instance must use the value `datacenter-ec2` as its Name tag, which defines the instance name in AWS.
2. Use the `Amazon Linux` `ami-0c101f26f147fa7fd` to launch this instance.
3. The Instance type must be `t2.micro`.
4. Create a new RSA key named `datacenter-kp`.
5. Attach the default (available by default) security group.

The Terraform working directory is `/home/bob/terraform`. Create the `main.tf` file (do not create a different `.tf` file) to provision the instance.

**Note:** Right-click under the `EXPLORER` section in `VS Code` and select `Open in Integrated Terminal` to launch the terminal.

---

## Solution Steps

### Step 1: Open the terminal and go to the working directory

In VS Code, right-click under **EXPLORER** → **Open in Integrated Terminal**, then run:

```bash
cd /home/bob/terraform
```

### Step 2: Check existing files

```bash
ls
cat provider.tf
```

The lab already provides `provider.tf` with the AWS provider block. **Do NOT add another `provider` block in `main.tf`**, otherwise you will get the "Duplicate provider configuration" error.

### Step 3: Create/edit `main.tf`

```bash
vi main.tf
```

Press `i` to enter insert mode and paste the following:

```hcl
# Generate a new RSA private key
resource "tls_private_key" "datacenter_key" {
  algorithm = "RSA"
  rsa_bits  = 4096
}

# Create the AWS key pair named datacenter-kp
resource "aws_key_pair" "datacenter_kp" {
  key_name   = "datacenter-kp"
  public_key = tls_private_key.datacenter_key.public_key_openssh
}

# Save the private key locally
resource "local_file" "datacenter_private_key" {
  content         = tls_private_key.datacenter_key.private_key_pem
  filename        = "/home/bob/terraform/datacenter-kp.pem"
  file_permission = "0400"
}

# Get the default security group
data "aws_security_group" "default" {
  name = "default"
}

# Create the EC2 instance
resource "aws_instance" "datacenter_ec2" {
  ami                    = "ami-0c101f26f147fa7fd"
  instance_type          = "t2.micro"
  key_name               = aws_key_pair.datacenter_kp.key_name
  vpc_security_group_ids = [data.aws_security_group.default.id]

  tags = {
    Name = "datacenter-ec2"
  }
}
```

Save and exit: press `Esc`, type `:wq`, press `Enter`.

### Step 4: Verify the file

```bash
cat main.tf
```

Make sure it starts with `resource "tls_private_key"...` and has **no** `provider` block.

### Step 5: Initialize Terraform

```bash
terraform init
```

### Step 6: Review the plan

```bash
terraform plan
```

You should see **4 resources to add** (tls key, key pair, local file, EC2 instance).

### Step 7: Apply the configuration

```bash
terraform apply
```

Type `yes` when asked to confirm.

### Step 8: Verify

```bash
terraform show
```

Check that the instance has:
- Name tag: `datacenter-ec2`
- Instance type: `t2.micro`
- Key name: `datacenter-kp`
- AMI: `ami-0c101f26f147fa7fd`
- Default security group attached

---

## What Each Part Does

| Resource | Purpose |
|----------|---------|
| `tls_private_key` | Creates a new RSA key (requirement 4) |
| `aws_key_pair` | Uploads the public key to AWS with the name `datacenter-kp` |
| `local_file` | Saves the private key locally so you can SSH later |
| `data "aws_security_group"` | Looks up the existing default security group (requirement 5) |
| `aws_instance` | Creates the EC2 instance with the right AMI, type, key, SG and Name tag |

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| **Duplicate provider configuration** | Remove the `provider "aws"` block from `main.tf` (it already exists in `provider.tf`) |
| **Multiple default SGs found** | Add a filter to the data block (see below) |
| **Key pair already exists** | Delete the existing key pair in AWS, or run `terraform destroy` and apply again |

Alternative security group lookup if multiple default SGs exist:

```hcl
data "aws_security_group" "default" {
  filter {
    name   = "group-name"
    values = ["default"]
  }
}
```