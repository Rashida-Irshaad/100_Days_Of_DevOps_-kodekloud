# Day 3 - Challenge 

Day 3 - Disable Direct Root SSH Login on All App Servers
Task

Following a recent security audit, the xFusionCorp Industries security team has introduced stricter security policies. One key requirement is to disable direct root SSH login on all App Servers within the Stratos Datacenter.

This ensures that no one can log in directly as the root user via SSH. Instead, users should use their own accounts and escalate privileges using sudo when necessary.

Infrastructure Details

Datacenter: Stratos

App Server 1: stapp01 → User: tony

App Server 2: stapp02 → User: steve

App Server 3: stapp03 → User: banner

Step-by-Step Solution
1. Connect to Each App Server

From the jump host (thor@jump_host), connect to each App Server:

ssh tony@stapp01      # App Server 1
ssh steve@stapp02     # App Server 2
ssh banner@stapp03    # App Server 3

2. Switch to Root User
sudo su -

3. Edit the SSH Configuration File

Open the SSH config file in the text editor:

vi /etc/ssh/sshd_config


Find the line:

#PermitRootLogin yes


Change it to:

PermitRootLogin no


If the line doesn’t exist, add it manually.

4. Save and Exit

Press Esc

Type :wq

Hit Enter

5. Restart the SSH Service

Apply the changes:

systemctl restart sshd

6. (Optional) Verify SSH Config Syntax

Before restarting SSH, check for syntax errors:

sshd -t


No output means the syntax is correct.

7. Repeat for All App Servers

Apply the same steps on all three servers:

stapp01

stapp02

stapp03

Final Verification

Run the following command on each App Server:

grep -i 'permitrootlogin' /etc/ssh/sshd_config


Expected output:

PermitRootLogin no
