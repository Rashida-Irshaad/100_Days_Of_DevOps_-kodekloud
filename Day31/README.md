# Day 31 - Challenge ### Task Description
The Nautilus application development team was working on a git repository `/usr/src/kodekloudrepos/official` present on the Storage server in Stratos DC. One of the developers stashed some in-progress changes in this repository, but now they want to restore some of the stashed changes.  

**Requirements:**
- Look for the stashed changes under `/usr/src/kodekloudrepos/official` git repository.  
- Restore the stash with identifier `stash@{1}`.  
- Commit and push the changes to the origin.  

---

### Solution Steps

1. **SSH into the Storage Server**
```bash
ssh natasha@ststor01
```

2. **Go to the repository**
```bash
cd /usr/src/kodekloudrepos/official
```

3. **Check stashes available**
```bash
git stash list
```
Output (example):
```
stash@{0}: WIP on master: cd64dc6 initial commit
stash@{1}: WIP on master: cd64dc6 initial commit
```

4. **Apply the stash with identifier `stash@{1}`**
```bash
sudo git stash apply stash@{1}
```
This restores the file(s). Example:
```
Changes to be committed:
  new file:   welcome.txt
```

5. **Stage the restored file**
```bash
sudo git add .
```

6. **Commit the changes**
```bash
sudo git commit -m "restored changes from stash@{1}"
```

7. **Push to the remote master branch**
```bash
sudo git push origin master
```

8. **Verify commit**
```bash
git log --oneline -1
```
Expected output:
```
<new-commit-hash> restored changes from stash@{1}
```


