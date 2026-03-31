# Day 25 - Challenge 
## Task Description:
The Nautilus application development team wants the following changes in the `/usr/src/kodekloudrepos/media` repository on the Storage Server in Stratos DC:

1. Create a new branch `nautilus` from `master`.  
2. Copy `/tmp/index.html` file into the repository.  
3. Add and commit the file in the `nautilus` branch.  
4. Merge `nautilus` branch back into `master`.  
5. Push changes for both branches (`master` and `nautilus`) to the origin.

---

## Solution Steps:

# Step 1: Connect to the Storage Server
ssh natasha@ststor01
# Enter the password when prompted.

---

# Step 2: Navigate to the repo directory
cd /usr/src/kodekloudrepos/media

---

# Step 3: Switch to master branch
sudo git checkout master

---

# Step 4: Create a new branch 'nautilus' from master
sudo git checkout -b nautilus
# The '-b' creates and switches to the new branch.

---

# Step 5: Copy the index.html file into the repo
sudo cp /tmp/index.html .

---

# Step 6: Add and commit the file in the nautilus branch
sudo git add index.html
sudo git commit -m "Add index.html file in nautilus branch"

---

# Step 7: Switch back to master branch
sudo git checkout master

---

# Step 8: Merge nautilus branch into master
sudo git merge nautilus

---

# Step 9: Push both branches to origin
sudo git push origin master
sudo git push origin nautilus

---

## Notes:
- The `sudo` ensures commands run without ownership permission errors.
- Merging ensures the new file is reflected in `master`.
- Pushing both branches keeps remote repo fully updated.

