# Day 22 - Challenge 
## Task Description:
The DevOps team established a new Git repository last week, which remains unused at present.  
The Nautilus application development team now requires a copy of this repository on the Storage Server in the Stratos DC.  

**Requirements:**
- The repository to be cloned is located at `/opt/apps.git`
- Clone this Git repository into `/usr/src/kodekloudrepos`
- Ensure no modifications are made to the repository during the cloning process.

---

## Solution Steps:

# Step 1: Connect to the Storage Server
ssh natasha@ststor01
# When prompted for authenticity, type 'yes' and enter the password for natasha.

---

# Step 2: Navigate to the target directory (parent directory)
cd /usr/src/kodekloudrepos

---

# Step 3: Clone the repository
sudo git clone /opt/apps.git
# Git will create a new folder named `apps` inside `/usr/src/kodekloudrepos`
# Expected output:
# Cloning into 'apps'...
# warning: You appear to have cloned an empty repository.
# done.

---
