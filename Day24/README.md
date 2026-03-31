# Day 24 - Challenge 
## Task Description:
The Nautilus developers want to implement new features in the `/usr/src/kodekloudrepos/official` repository while keeping the master branch unchanged.  
The requirement is to **create a new branch** named `xfusioncorp_official` from the master branch on the Storage Server in Stratos DC **without modifying any code**.

---

## Solution Steps:

# Step 1: Connect to the Storage Server
ssh natasha@ststor01
# When prompted for authenticity, type 'yes' and enter the password for natasha.

---

# Step 2: Navigate to the official repository
cd /usr/src/kodekloudrepos/official/

---

# Step 3: Switch to the master branch
sudo git checkout master
# Output confirms:
# Switched to branch 'master'
# Your branch is up to date with 'origin/master'.

---

# Step 4: Create a new branch from master
sudo git branch xfusioncorp_official

---

# Step 5: Verify the new branch
sudo git branch
# Output shows:
#   kodekloud_official
# * master
#   xfusioncorp_official
# The * indicates the currently checked-out branch.

---

## Notes:
- Using `sudo` ensures commands bypass ownership restrictions on the repository.
- The new branch `xfusioncorp_official` is an exact copy of master.
- No code changes were made during branch creation.
- Developers can now safely commit new features to `xfusioncorp_official`.

