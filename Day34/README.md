# Day 34 - Challenge 
### Task Description:
The Nautilus application development team was working on a git repository `/opt/cluster.git` which is cloned under `/usr/src/kodekloudrepos` on the Storage server in Stratos DC. The team wants to set up a hook on this repository.

**Requirements:**
1. Merge the `feature` branch into the `master` branch.
2. Before pushing, create a `post-update` hook in this git repository so that whenever any changes are pushed to the master branch, it creates a release tag with the format `release-YYYY-MM-DD` (today’s date).
3. Test the hook at least once to make sure it creates the release tag for today.
4. Finally, push the changes.

---

### Solution Steps:

1. **SSH into the Storage server**:
   ```bash
   ssh natasha@ststor01
   ```

2. **Go to the cloned working repository**:
   ```bash
   cd /usr/src/kodekloudrepos/cluster
   ```

3. **Checkout the master branch and merge feature into it**:
   ```bash
   git checkout master
   git pull origin master
   git merge feature
   ```

4. **Now create the post-update hook in the bare repo**:
   ```bash
   cd /opt/cluster.git/hooks
   sudo vi post-update
   ```

   Add the following content inside `post-update`:
   ```bash
   #!/bin/bash
   REPO_PATH="/opt/cluster.git"
   DATE=$(date +%F)   # formats as YYYY-MM-DD
   cd $REPO_PATH
   branch=$(git rev-parse --abbrev-ref HEAD)
   if [ "$branch" == "master" ]; then
       git tag -f release-$DATE
   fi
   ```

5. **Make the hook executable**:
   ```bash
   sudo chmod +x post-update
   ```

6. **Go back to working repo and push the changes**:
   ```bash
   cd /usr/src/kodekloudrepos/cluster
   git push origin master
   ```

7. **Verify that the release tag was created**:
   ```bash
   cd /opt/cluster.git
   git tag
   ```

   You should see something like:
   ```
   release-2025-09-07
   ```

✅ Done! The `feature` branch is merged into `master`, a `post-update` hook ensures new pushes create a daily release tag, and today’s release tag has been created successfully.

