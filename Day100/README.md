# Day 100 - Challenge 
# KodeKloud Task: EC2 Instance + CloudWatch Alarm with Terraform

## Task Description

The Nautilus DevOps team needs an EC2 instance for their application, plus a CloudWatch alarm to monitor its CPU utilization. The alarm must trigger if CPU utilization exceeds 90% for one consecutive 5-minute period. Notifications go to the SNS topic `nautilus-sns-topic`, which is already created.

1. **Launch EC2 Instance:** Create an EC2 instance named `nautilus-ec2` using an Ubuntu AMI (`ami-0c02fb55956c7d316`).
2. **Create CloudWatch Alarm:** Create an alarm named `nautilus-alarm` with:
   * Statistic: Average
   * Metric: CPUUtilization
   * Threshold: >= 90% for 1 consecutive 5-minute period
   * Alarm Action: send a notification to the `nautilus-sns-topic` SNS topic
3. Update the existing `main.tf` file (do not create a separate .tf file) to create the EC2 instance and CloudWatch alarm.
4. Create an `outputs.tf` file with these outputs:
   * `KKE_instance_name` for the EC2 instance name
   * `KKE_alarm_name` for the CloudWatch alarm name

**Notes:**
* The Terraform working directory is `/home/bob/terraform`.
* Open the terminal in VS Code: right-click in EXPLORER, then **Open in Integrated Terminal**.
* Before submitting, `terraform plan` must return: `No changes. Your infrastructure matches the configuration.`

---

## Solution Steps

### Step 1: Go to the working directory and check existing files

```bash
cd /home/bob/terraform
ls
cat main.tf
grep -rn "provider" *.tf
```

Expected: `main.tf` already contains only the SNS topic resource:

```hcl
resource "aws_sns_topic" "sns_topic" {
  name = "nautilus-sns-topic"
}
```

Also, `provider.tf` already has the `provider "aws"` block, so **do not** add another provider block in `main.tf`.

### Step 2: Add the EC2 instance and CloudWatch alarm to `main.tf`

This command appends to the end of the file and keeps the existing SNS topic:

```bash
cat >> main.tf << 'EOF'

resource "aws_instance" "nautilus_ec2" {
  ami           = "ami-0c02fb55956c7d316"
  instance_type = "t2.micro"

  tags = {
    Name = "nautilus-ec2"
  }
}

resource "aws_cloudwatch_metric_alarm" "nautilus_alarm" {
  alarm_name          = "nautilus-alarm"
  comparison_operator = "GreaterThanOrEqualToThreshold"
  evaluation_periods  = 1
  metric_name         = "CPUUtilization"
  namespace           = "AWS/EC2"
  period              = 300
  statistic           = "Average"
  threshold           = 90

  dimensions = {
    InstanceId = aws_instance.nautilus_ec2.id
  }

  alarm_actions = [aws_sns_topic.sns_topic.arn]
}
EOF
```

### Step 3: Verify `main.tf`

```bash
cat main.tf
grep -rn "provider" *.tf
```

* `main.tf` must contain 3 resources: `aws_sns_topic`, `aws_instance`, `aws_cloudwatch_metric_alarm`.
* `provider "aws"` must appear **only in `provider.tf`**, not in `main.tf`.

### Step 4: Create `outputs.tf`

```bash
cat > outputs.tf << 'EOF'
output "KKE_instance_name" {
  value = aws_instance.nautilus_ec2.tags["Name"]
}

output "KKE_alarm_name" {
  value = aws_cloudwatch_metric_alarm.nautilus_alarm.alarm_name
}
EOF
```

### Step 5: Initialize and plan

```bash
terraform init
terraform plan
```

Expected result:

```
Plan: 2 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + KKE_alarm_name    = "nautilus-alarm"
  + KKE_instance_name = "nautilus-ec2"
```

### Step 6: Apply

```bash
terraform apply
```

Type `yes` when asked. Wait for:

```
Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```

### Step 7: Final verification

```bash
terraform plan
terraform output
```

Expected:

```
No changes. Your infrastructure matches the configuration.
```

```
KKE_alarm_name = "nautilus-alarm"
KKE_instance_name = "nautilus-ec2"
```

Then click **Submit** on the task page.

---

## Key Settings Explained

| Setting | Meaning |
|---|---|
| `period = 300` | 5-minute period (300 seconds) |
| `evaluation_periods = 1` | 1 consecutive period |
| `statistic = "Average"` | Average of the metric |
| `GreaterThanOrEqualToThreshold` | The `>= 90%` condition |
| `alarm_actions = [aws_sns_topic.sns_topic.arn]` | Sends the notification to the SNS topic |

## Common Problems and Fixes

| Problem | Fix |
|---|---|
| `Reference to undeclared resource` | Resources are missing from `main.tf`. Re-run Step 2 and check with `cat main.tf`. |
| Duplicate provider error | Remove the `provider "aws"` block from `main.tf` (e.g. `sed -i '5,7d' main.tf` if it sits on lines 5-7). Keep only the one in `provider.tf`. |
| Plan wants to create the SNS topic | Switch to a `data "aws_sns_topic"` block instead of the resource. |
