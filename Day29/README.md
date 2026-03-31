# Day 29 - Challenge 
## Task: Merge Max's Story to Master via Pull Request (PR) Workflow

Max has pushed his story about "The 🦊 Fox and Grapes 🍇" to the `story/fox-and-grapes` branch in the `sarah/story-blog` repository. The goal is to merge it into `master` using a Pull Request workflow, ensuring review before merging. Max cannot push directly to master; all changes must be reviewed and approved. The steps are as follows:

1. SSH into the storage server as Max: `ssh max@ststor01` and enter password `Max_pass123`. 2. Navigate to Max’s cloned repository and verify commits: `cd /home/max/story-blog; git branch; git log --oneline --decorate --graph --all`. Confirm that the branch `story/fox-and-grapes` exists and contains the commit `Added fox-and-grapes story`. 3. Open the Gitea UI, login as Max (Username: `max`, Password: `Max_pass123`), go to the repository `sarah/story-blog`, click **Pull Requests → New Pull Request**, set **Source branch:** `story/fox-and-grapes`, **Destination branch:** `master`, set PR title: `Added fox-and-grapes story`, and submit the PR. 4. Assign Tom as a reviewer: In the PR page, on the right sidebar under **Reviewers**, add `tom` as reviewer. 5. Logout Max, login as Tom (Username: `tom`, Password: `Tom_pass123`), open the PR titled `Added fox-and-grapes story`, click **Review → Approve**, optionally add a comment, and submit the review. 6. Merge the PR: Once approved, click **Merge → Create a merge commit → Confirm**. The PR is now merged into `master`.



