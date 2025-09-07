# Day 33 - Challenge 
### Task Description
Sarah and Max were working on writing some stories which they have pushed to the repository. Max has recently added some new changes and is trying to push them to the repository but he is facing some issues. Below you can find more details:

- SSH into storage server using user **max** and password **Max_pass123**.  
- Under `/home/max` you will find the **story-blog** repository.  
- Try to push the changes to the origin repo and fix the issues.  
- The `story-index.txt` must have titles for **all 4 stories**.  
- Additionally, there is a typo in `The Lion and the Mooose` line where **Mooose** should be **Mouse**.  

Click on the **Gitea UI button** on the top bar. You should be able to access the Gitea page.  
You can login to Gitea server from UI using:  
- Username: **sarah**, Password: **Sarah_pass123**  
- or Username: **max**, Password: **Max_pass123**

> Note: For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as [loom.com](http://loom.com) to record and share your work.

---

### Solution Steps
```bash
# 1. SSH into storage server as user max
ssh max@ststor01
# Password: Max_pass123

# 2. Go to repository
cd /home/max/story-blog

# 3. Check current branch and status
git status
git branch

# 4. If you see "(no branch, rebasing master)", finish rebase:
nano story-index.txt     # add titles for all 4 stories + fix typo Mooose → Mouse
git add story-index.txt
git rebase --continue

# 5. Switch back to master branch
git checkout master

# 6. Verify file contents
cat story-index.txt

# 7. Stage and commit changes if needed
git add story-index.txt
git commit -m "fixed story index and corrected typo"

# 8. Push changes to origin (force push if rebase was done)
git push origin master --force

