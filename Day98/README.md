# Day 98 - Challenge # KodeKloud Task: Private VPC, Subnet and EC2 with Terraform

## Task Description

The Nautilus DevOps team is expanding their AWS infrastructure and requires the setup of a private Virtual Private Cloud (VPC) along with a subnet. This VPC and subnet configuration will ensure that resources deployed within them remain isolated from external networks and can only communicate within the VPC. Additionally, the team needs to provision an EC2 instance under the newly created private VPC. This instance should be accessible only from within the VPC, allowing for secure communication and resource management within the AWS environment.

### Requirements

1. Create a VPC named `devops-priv-vpc` with the CIDR block `10.0.0.0/16`.
2. Create a subnet named `devops-priv-subnet` inside the VPC with the CIDR block `10.0.1.0/24` and the `auto-assign` IP option must not be enabled.
3. Create an EC2 instance named `devops-priv-ec2` inside the subnet. The instance type must be `t2.micro`.
4. Ensure the security group of the EC2 instance allows access only from within the VPC's CIDR block.
5. Create the `main.tf` file (do not create a separate `.tf` file) to provision the VPC, subnet and EC2 instance.
6. Use the `variables.tf` file with these variable names:
   - `KKE_VPC_CIDR` for the VPC CIDR block.
   - `KKE_SUBNET_CIDR` for the subnet CIDR block.
7. Use the `outputs.tf` file with these output names:
   - `KKE_vpc_name` for the name of the VPC.
   - `KKE_subnet_name` for the name of the subnet.
   - `KKE_ec2_private` for the name of the EC2 instance.

### Notes

1. The Terraform working directory is `/home/bob/terraform`.
2. Right-click under the `EXPLORER` section in VS Code and select `Open in Integrated Terminal` to launch the terminal.
3. Before submitting the task, ensure that `terraform plan` returns `No changes. Your infrastructure matches the configuration.`

---

## Solution

### Step 1: Open the terminal and go to the working directory

In VS Code, right-click under EXPLORER and choose "Open in Integrated Terminal". Then run:

```bash
cd /home/bob/terraform
ls
```

Check whether files like `provider.tf` already exist. If a provider block is already defined, do not duplicate it in `main.tf`.

### Step 2: Create `variables.tf`

```bash
cat > variables.tf << 'EOF'
variable "KKE_VPC_CIDR" {
  type    = string
  default = "10.0.0.0/16"
}

variable "KKE_SUBNET_CIDR" {
  type    = string
  default = "10.0.1.0/24"
}
EOF
```

### Step 3: Create `main.tf`

```bash
cat > main.tf << 'EOF'
provider "aws" {
  region = "us-east-1"
}

# 1. VPC
resource "aws_vpc" "devops_priv_vpc" {
  cidr_block = var.KKE_VPC_CIDR

  tags = {
    Name = "devops-priv-vpc"
  }
}

# 2. Subnet (auto-assign public IP disabled)
resource "aws_subnet" "devops_priv_subnet" {
  vpc_id                  = aws_vpc.devops_priv_vpc.id
  cidr_block              = var.KKE_SUBNET_CIDR
  map_public_ip_on_launch = false

  tags = {
    Name = "devops-priv-subnet"
  }
}

# 4. Security group: allow traffic only from inside the VPC CIDR
resource "aws_security_group" "devops_priv_sg" {
  name        = "devops-priv-sg"
  description = "Allow access only from within the VPC"
  vpc_id      = aws_vpc.devops_priv_vpc.id

  ingress {
    description = "All traffic from VPC CIDR"
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = [var.KKE_VPC_CIDR]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = [var.KKE_VPC_CIDR]
  }

  tags = {
    Name = "devops-priv-sg"
  }
}

# AMI lookup
data "aws_ami" "amazon_linux" {
  most_recent = true
  owners      = ["amazon"]

  filter {
    name   = "name"
    values = ["amzn2-ami-hvm-*-x86_64-gp2"]
  }
}

# 3. EC2 instance
resource "aws_instance" "devops_priv_ec2" {
  ami                    = data.aws_ami.amazon_linux.id
  instance_type          = "t2.micro"
  subnet_id              = aws_subnet.devops_priv_subnet.id
  vpc_security_group_ids = [aws_security_group.devops_priv_sg.id]

  tags = {
    Name = "devops-priv-ec2"
  }
}
EOF
```

Note: if the lab gives a specific AMI ID or region, or an existing `.tf` file already defines the provider or AMI, reuse that instead.

### Step 4: Create `outputs.tf`

```bash
cat > outputs.tf << 'EOF'
output "KKE_vpc_name" {
  value = aws_vpc.devops_priv_vpc.tags["Name"]
}

output "KKE_subnet_name" {
  value = aws_subnet.devops_priv_subnet.tags["Name"]
}

output "KKE_ec2_private" {
  value = aws_instance.devops_priv_ec2.tags["Name"]
}
EOF
```

### Step 5: Initialize, validate and apply

```bash
terraform init
terraform validate
terraform apply -auto-approve
```

### Step 6: Verify

```bash
terraform plan
```

Expected result:

```
No changes. Your infrastructure matches the configuration.
```

Optionally check the outputs:

```bash
terraform output
```

---

## Troubleshooting

| Problem | Fix |
|---|---|
| AMI not found | Run `aws ec2 describe-images --owners amazon --max-items 5` and hardcode a valid AMI ID in `main.tf` |
| Provider or region error | Check for an existing `provider.tf` or the region given in the lab, and match it |
| `terraform plan` shows changes after apply | Re-run `terraform apply -auto-approve`, then plan again |
| Duplicate provider error | Remove the `provider "aws"` block from `main.tf` if one exists in another file |

