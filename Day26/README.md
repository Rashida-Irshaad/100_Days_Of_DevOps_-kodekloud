# Day 26 - Challenge 
## Task Description:
The xFusionCorp development team needs to update the Git remotes and push changes in the `/usr/src/kodekloudrepos/ecommerce` repository on the Storage server in Stratos DC.  

Requirements:
1. Add a new remote named `dev_ecommerce` pointing to `/opt/xfusioncorp_ecommerce.git`.  
2. Copy `/tmp/index.html` into the repo and commit the changes to the `master` branch.  
3. Push the `master` branch to the new remote `dev_ecommerce`.

---

## Solution Steps:

# Step 1: Connect to the Storage Server
ssh natasha@ststor01
# Enter the password when prompted.

---

# Step 2: Navigate to the ecommerce repo
cd /usr/src/kodekloudrepos/ecommerce

---

# Step 3: Add the new remote named 'dev_ecommerce'
sudo git remote add dev_ecommerce /opt/xfusioncorp_ecommerce.git

# Verify the remote
sudo git remote -v
# Expected output should show dev_ecommerce pointing to /opt/xfusioncorp_ecommerce.git

---

# Step 4: Copy index.html into the repo
sudo cp /tmp/index.html .

---

# Step 5: Add and commit the file to master branch
sudo git add index.html
sudo git commit -m "Add index.html file to master branch"

---

# Step 6: Push the master branch to the new remote
sudo git push dev_ecommerce master

---

## Notes:
- `sudo` ensures commands run without permission errors.
- The new remote `dev_ecommerce` now holds the updated master branch with `index.html`.
- You can confirm by checking the `/opt/xfusioncorp_ecommerce.git` repo after push.

