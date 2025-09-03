# Day 27 - Challenge 
## Task Description:
The Nautilus application development team reported an issue with the latest commit in the `/usr/src/kodekloudrepos/demo` repository on the Storage server in Stratos DC.  
The goal is to:
1. Revert the latest commit (HEAD) to the previous commit (with the initial commit message).  
2. Use the commit message `revert demo` (all lowercase) for the revert commit.

---

## Solution Steps:

# Step 1: Connect to the Storage Server
ssh natasha@ststor01
# Enter the password when prompted.

---

# Step 2: Navigate to the demo repo
cd /usr/src/kodekloudrepos/demo

---

# Step 3: Revert the latest commit (HEAD) to the previous commit
sudo git revert HEAD --no-edit

# By default, git will use a generated message.
# We'll amend the commit message in the next step.

---

# Step 4: Change the commit message to "revert demo"
sudo git commit --amend -m "revert demo"

---

# Step 5: Verify commit history
sudo git log --oneline
# The top commit should now show: revert demo

---

# Step 6: Push the changes to the remote repository
sudo git push origin master

---

## Notes:
- `git revert` creates a new commit that undoes the latest commit without rewriting history.
- `git reset` would have removed the commit, but it wasn't requested here.
- Using `--no-edit` skips opening an editor; we amended the message manually.

