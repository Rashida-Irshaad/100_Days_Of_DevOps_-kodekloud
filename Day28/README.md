# Day 28 - Challenge 
## Task Description:
The Nautilus application development team wants to merge a single commit with the message **"Update info.txt"** from the `feature` branch into the `master` branch in the `/usr/src/kodekloudrepos/news` repository on the Storage server in Stratos DC.  
After merging, the changes need to be pushed to the remote repository.

---

## Solution Steps:

# Step 1: Connect to the Storage Server
ssh natasha@ststor01
# Enter password when prompted.

---

# Step 2: Navigate to the news repository
cd /usr/src/kodekloudrepos/news

---

# Step 3: Ensure we are on the master branch
sudo git checkout master

---

# Step 4: Find the commit hash in feature branch with the message "Update info.txt"
sudo git log feature --oneline
# Look for the commit message "Update info.txt" and note its commit hash (e.g., abc1234).

---

# Step 5: Cherry-pick the commit into master branch
sudo git cherry-pick <commit-hash>
# Replace <commit-hash> with the actual hash you found in Step 4.

---

# Step 6: Push the updated master branch to the remote repository
sudo git push origin master

---

## Notes:
- Cherry-pick merges only the specific commit instead of the entire branch.
- If there are conflicts, resolve them, then run:
  sudo git add .
  sudo git cherry-pick --continue
- After completion, the `master` branch will include only the required commit from `feature`.

