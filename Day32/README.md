# Day 32 - Challenge 
# TASK DESCRIPTION:
# The Nautilus application development team has a project repository /opt/official.git, cloned at /usr/src/kodekloudrepos on the Storage server in Stratos DC.
# A developer’s work is on the 'feature' branch, while new commits landed on 'master'.
# Requirement: Rebase 'feature' on top of 'master' (no merge commit), without losing feature changes, and push the updated branch.

# SOLUTION STEPS (including SSH):

# 1) SSH to the Storage server
ssh natasha@ststor01
# (enter password when prompted)

# 2) Go to the cloned repo
cd /usr/src/kodekloudrepos/official

# 3) Ensure repo is clean and see remotes
git status
git remote -v

# 4) Make sure we have the latest from the server
git fetch origin

# 5) Switch to the feature branch
git checkout feature

# 6) Rebase feature onto the latest master without creating a merge commit
git rebase origin/master

#    If there are conflicts:
#    - Check conflicted files:
#      git status
#    - Fix files, then stage them:
#      git add <file1> <file2> ...
#    - Continue the rebase:
#      git rebase --continue
#    - If you need to abort for any reason:
#      git rebase --abort

# 7) Push the rebased branch to the remote (history was rewritten, so force-with-lease is required)
git push origin feature --force-with-lease

# 8) (Optional) Verify the history shows feature commits on top of master, with no merge commit
git log --oneline --graph --decorate --all --max-count=30

