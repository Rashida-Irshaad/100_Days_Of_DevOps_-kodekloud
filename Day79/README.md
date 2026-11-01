# Day 79 - Challenge 
# KodeKloud Task: Jenkins Auto-Deploy Job (`nautilus-app-deployment`)

## Task Description

The Nautilus development team had a meeting with the DevOps team where they discussed automating the deployment of one of their apps using Jenkins (the one in `Stratos Datacenter`). They want to auto deploy the new changes in case any developer pushes to the repository. As per the requirements below, configure the required Jenkins job.

Access Jenkins using the `Jenkins` button on the top bar. Login with username `admin` and password `Adm!n321`.

Access Gitea using the `Gitea` button. Username and password for Git are `sarah` and `Sarah_pass123`. Under user `sarah` there is a repository named `web`, already cloned on App Server 1 under sarah's home (`/home/sarah/web`). `sarah` is a developer working on this repository.

### Requirements

1. `httpd` is already installed and configured on the app server (listening on port `8080`). Ensure the `httpd` service is running on App Server 1. Starting/restarting httpd can be part of the Jenkins job if preferred.
2. Create a Jenkins job named `nautilus-app-deployment` and configure it so that if anyone pushes any new change to the origin repository in the `master` branch, the job auto builds and deploys the latest code on App Server 1 under `/var/www/html`.
   Before deployment, ensure the ownership of `/var/www/html` is set to user `sarah`, so Jenkins can deploy files to that directory.
3. SSH into App Server 1 as `sarah`. Under `/home/sarah/web` there is a cloned Git repository named `web` with an `index.html` file. Update its content to `Welcome to the xFusionCorp Industries`, then push the changes to `origin` on the `master` branch. This push must trigger the Jenkins job, and the latest changes must be deployed on the server. The entire repository content must be deployed, not only `index.html`.

Click the `App` button on the top bar to access the app. The required content must load on the main URL (e.g. `http://stlb01:8091`), with no sub-directory such as `/web`.

### Notes

1. You might need to install plugins and restart Jenkins. Click `Restart Jenkins when installation is complete and no jobs are running` on the update centre page. If the Jenkins UI gets stuck after a restart, refresh the page.
2. Make sure the Jenkins job passes even on repetitive runs, as validation may build the job multiple times.
3. Deployment tasks should be done by a `sudo` user on the destination server to avoid permission issues.
4. For web UI tasks, take screenshots to share for review if the task is marked incomplete.

---

## Solution

### Part 1: Prepare App Server 1

SSH in as the sudo user (in Stratos this is normally `tony`):

```bash
ssh tony@stapp01
```

Start httpd:

```bash
sudo systemctl start httpd
sudo systemctl enable httpd
sudo systemctl status httpd
```

It should show `active (running)`.

Give `sarah` ownership of the web folder:

```bash
sudo chown -R sarah:sarah /var/www/html
ls -ld /var/www/html
```

The owner should now be `sarah sarah`.

### Part 2: Install Jenkins Plugins

1. Open Jenkins and log in with `admin` / `Adm!n321`.
2. Go to **Manage Jenkins -> Plugins -> Available plugins**.
3. Search for and install:
   - **Git**
   - **Publish Over SSH**
4. Tick **Restart Jenkins when installation is complete and no jobs are running**.
5. After the restart, refresh the page and log in again.

Skip any plugin that is already installed.

### Part 3: Connect Jenkins to App Server 1

1. Go to **Manage Jenkins -> System**.
2. Scroll to **Publish over SSH**.
3. Under **SSH Servers**, click **Add** and fill in:
   - **Name:** `stapp01`
   - **Hostname:** `stapp01`
   - **Username:** `sarah`
   - **Remote Directory:** `/var/www/html`
4. Click **Advanced**, tick **Use password authentication**, and enter `Sarah_pass123`.
5. Click **Test Configuration**. It must say **Success**.
6. Click **Save**.

### Part 4: Create the Jenkins Job

1. Click **New Item**, name it `nautilus-app-deployment`, choose **Freestyle project**, and click **OK**.

2. **Source Code Management**
   - Select **Git**.
   - **Repository URL:** in Gitea open `sarah/web`, click the blue **Code** button, make sure **HTTP** is selected, and copy the URL. It looks like:
     ```
     http://git.stratos.xfusioncorp.com/sarah/web.git
     ```
   - **Credentials:** click **Add -> Jenkins**, enter Username `sarah` and Password `Sarah_pass123`, click **Add**, then select it in the dropdown.
   - **Branch Specifier:** `*/master`

3. **Build Triggers**
   - Tick **Poll SCM**.
   - Schedule: `* * * * *` (checks for new pushes every minute).

4. **Post-build Actions -> Send build artifacts over SSH**
   - **SSH Server Name:** `stapp01`
   - **Source files:** `**`
   - **Exclude files:** `.git/**`
   - **Remove prefix:** leave empty
   - **Remote directory:** leave empty (it uses `/var/www/html` from Part 3)

   This copies the whole repository into `/var/www/html` with no sub-folder and overwrites existing files, so repeated builds pass.

5. Click **Save**, then **Build Now**. Open the build and check **Console Output**. It must end with `Finished: SUCCESS`.

### Part 5: Update index.html and Push

SSH to App Server 1 as sarah:

```bash
ssh sarah@stapp01
cd /home/sarah/web
```

Update the file:

```bash
echo "Welcome to the xFusionCorp Industries" > index.html
cat index.html
```

Commit and push:

```bash
git add .
git commit -m "Update index.html"
git push origin master
```

Enter `sarah` / `Sarah_pass123` if asked. Wait about one minute. Jenkins should start a new build automatically and finish with **SUCCESS**.

### Part 6: Verify

```bash
curl http://stapp01:8080
ls -la /var/www/html
sudo systemctl status httpd
```

- `curl` should print: `Welcome to the xFusionCorp Industries`
- `ls` should show `index.html` and all other repo files directly inside `/var/www/html`, owned by `sarah`.
- httpd should be `active (running)`.

Then click the **App** button on the top bar. The text should appear on the main URL with no `/web` at the end.

### Final Checklist

- [ ] Latest builds show SUCCESS and the console output ends with `Finished: SUCCESS`
- [ ] A build was triggered automatically after `git push` (Started by an SCM change)
- [ ] Clicking **Build Now** again still passes
- [ ] `/var/www/html` is owned by `sarah`
- [ ] Files are deployed directly in `/var/www/html`, not in a `web` sub-folder
- [ ] App URL shows `Welcome to the xFusionCorp Industries`
- [ ] httpd is running

---

## Troubleshooting

| Problem | Fix |
|---|---|
| Test Configuration fails | Re-check hostname `stapp01`, username `sarah`, and the password |
| Permission denied on deploy | Re-run `sudo chown -R sarah:sarah /var/www/html` as `tony` |
| Git step fails in Jenkins | Check the Gitea URL and the sarah credentials |
| Build does not start after push | Check Poll SCM is `* * * * *` and the branch is `*/master`; wait 1-2 minutes |
| Old page showing | Confirm the build succeeded, then `sudo systemctl restart httpd` as `tony` |
| Files land in a sub-folder | Leave **Remote directory** and **Remove prefix** empty |