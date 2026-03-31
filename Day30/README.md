# Day 30 - Challenge 
### Task Description
The Nautilus application development team was working on a git repository `/usr/src/kodekloudrepos/blog` present on the Storage server in Stratos DC. This was just a test repository, and one of the developers pushed a couple of changes for testing. Now they want to clean this repository along with the commit history/work tree, so they want to point back the HEAD and the branch itself to the commit with message **"add data.txt file"**.

**Requirements:**
- In `/usr/src/kodekloudrepos/blog` git repository, reset the git commit history so that there are only two commits in the commit history:
  1. initial commit
  2. add data.txt file
- Push the changes back to the remote.

---

### Solution Steps

1. SSH into Storage Server
```
ssh natasha@ststor01
```

2. Go to the blog repository
```
cd /usr/src/kodekloudrepos/blog
```

3. Check commit history to identify the commit hash for `add data.txt file`
```
git log --oneline
```
Example output:
```
f7dc729 Test Commit1
17c6f8c add data.txt file
6f2381e initial commit
```
Here, `17c6f8c` is the commit hash we need.

4. Reset the repo to that commit
```
sudo git reset --hard 17c6f8c
```

5. Force push the changes to remote master branch
```
sudo git push origin master --force
```

6. Verify commit history
```
git log --oneline
```
Expected output:
```
17c6f8c add data.txt file
6f2381e initial commit
```
