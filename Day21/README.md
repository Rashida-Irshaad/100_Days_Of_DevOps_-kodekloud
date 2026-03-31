# Day 21 - Challenge 
## Task Description:
The Nautilus development team requested the DevOps team to create a Git repository for a new application development project on the Storage Server in the Stratos DC.  
The requirements are:
1. Use `yum` to install the Git package on the Storage Server.
2. Create a **bare repository** named `/opt/demo.git` (ensure the exact name is used).

## Solution Steps:

# Step 1: Connect to the Storage Server
ssh natasha@ststor01
# When prompted for authenticity, type 'yes' and enter the password for natasha.

# Step 2: Install Git using yum
sudo yum install -y git

# Step 3: Create a bare Git repository in /opt
sudo git init --bare /opt/demo.git

# Step 4: Verify repository creation
ls -ld /opt/demo.git
# Expected output example:
# drwxr-xr-x 7 root root 4096 Sep  2 18:59 /opt/demo.git/

# Step 5: Check repository files
cd /opt/demo.git
ls
# Expected files: HEAD  branches  config  description  hooks  info  objects  refs


## Repository Details:
# - Repository path: /opt/demo.git
# - Type: Bare Repository (used for pushing/pulling code, not for direct coding)

