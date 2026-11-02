# Day 80 - Challenge 
# KodeKloud Task: Jenkins Chained Builds (Restart Apache After Deployment)

## Task Description

The DevOps team wants to restart the Apache service on all app servers only if the deployment goes fine on these servers in the Stratos Datacenter. After discussion, they decided to use **Jenkins chained builds**, so that a downstream job for services is triggered only by the deployment job.

### Requirements

1. Create a Jenkins job named `devops-app-deployment` and configure it to pull changes from the `master` branch of the `web` repository on App Server 1 under the `/var/www/html` directory.
2. Create another Jenkins job named `manage-services` and make it a **downstream job** for `devops-app-deployment`:
   - a. This job should restart the `httpd` service on App Server 1.
   - b. Trigger this job only if the upstream job `devops-app-deployment` is **stable**.
3. The LB server is already configured. The app must load on the main URL (e.g. `http://stlb01:8091`) with **no sub-directory** such as `/web`.

### Lab Details

| Item | Value |
|---|---|
| Jenkins login | `admin` / `Adm!n321` |
| Gitea login | `sarah` / `Sarah_pass123` |
| Gitea port | `3000` |
| Repository | `web` (under user `sarah`) |
| App Server 1 | `stapp01` (user `tony`) |
| Doc root | `/var/www/html` (local git repo tracking `origin`) |

### Notes

- Plugins may need to be installed, followed by a Jenkins restart.
- The jobs must pass even on repeated runs.
- Deployment tasks must be done with `sudo` on the destination server to avoid permission issues.

---

## Solution

### Step 1: Install Required Plugins

1. Open the Jenkins UI and log in with `admin` / `Adm!n321`.
2. Go to **Manage Jenkins -> Plugins -> Available plugins**.
3. Search for and select:
   - **Publish Over SSH**
   - **Git** (usually already installed)
4. Click **Install** and tick **Restart Jenkins when installation is complete and no jobs are running**.
5. Refresh the page and log in again once Jenkins is back.

### Step 2: Configure the SSH Server in Jenkins

1. Go to **Manage Jenkins -> System**.
2. Scroll to **Publish over SSH** and click **Add** under SSH Servers.
3. Fill in:
   - **Name:** `app1`
   - **Hostname:** `stapp01`
   - **Username:** `tony`
   - **Remote Directory:** `/`
4. Click **Advanced**, tick **Use password authentication**, and enter tony's password.
5. Click **Test Configuration**. It should return `Success`.
6. Click **Save**.

### Step 3: Verify the Repo and Sudo on App Server 1

```bash
ssh tony@stapp01

# Check passwordless sudo
sudo -n true && echo OK

# Check the repo
cd /var/www/html
git status
git remote -v
ls /var/www/html     # index.html must be directly here, not inside a "web" folder
```

If git reports "dubious ownership":

```bash
sudo git config --system --add safe.directory /var/www/html
```

If `git pull` asks for credentials, embed Sarah's credentials in the remote URL (keep the host and port shown by `git remote -v`):

```bash
cd /var/www/html
sudo git remote set-url origin http://sarah:Sarah_pass123@<gitea-host>:<port>/sarah/web.git
```

### Step 4: Create Job 1 `devops-app-deployment`

1. Go to **Dashboard -> New Item**.
2. Name: `devops-app-deployment`, type: **Freestyle project**, click **OK**.
3. Under **Build Steps**, add **Send files or execute commands over SSH**:
   - **SSH Server Name:** `app1`
   - **Exec command:**

```bash
cd /var/www/html && sudo git pull origin master
```

4. Click **Save**.

### Step 5: Create Job 2 `manage-services`

1. Go to **Dashboard -> New Item**.
2. Name: `manage-services`, type: **Freestyle project**, click **OK**.
3. Under **Build Triggers**, tick **Build after other projects are built**:
   - **Projects to watch:** `devops-app-deployment`
   - Select **Trigger only if build is stable**
4. Under **Build Steps**, add **Send files or execute commands over SSH**:
   - **SSH Server Name:** `app1`
   - **Exec command:**

```bash
sudo systemctl restart httpd
```

5. Click **Save**.

### Step 6: Run and Verify

1. Open `devops-app-deployment` and click **Build Now**.
2. Check **Console Output**. It should end with `Finished: SUCCESS`.
3. Open `manage-services`. A new build should have been triggered automatically and should also be successful.
4. The **Downstream Projects** section of `devops-app-deployment` lists `manage-services`.
5. Click the **App** button. The site loads on the main URL with no sub-directory.
6. Click **Build Now** again to confirm the jobs still pass on repeated runs (`git pull` returns "Already up to date" with exit code 0).

---

## Troubleshooting

| Problem | Fix |
|---|---|
| `sudo: a password is required` | Confirm passwordless sudo for `tony` with `sudo -n true` |
| Git "dubious ownership" error | Run the `safe.directory` command from Step 3 |
| `manage-services` does not trigger | Make sure job 1 is **Stable** and the trigger setting in job 2 is saved |
| SSH test fails | Re-check hostname, username, and password in the lab credentials |
| Jenkins UI stuck after restart | Refresh the browser page |

---

## Result

- `devops-app-deployment` pulls the latest `master` branch into `/var/www/html` on App Server 1.
- `manage-services` runs automatically as a downstream job and restarts `httpd`, only when the upstream build is stable.
- The application loads on the main URL (e.g. `http://stlb01:8091`) without any sub-directory.

**Task status: Completed**

## Concepts Covered

`Jenkins` `Chained Builds` `Upstream/Downstream Jobs` `Publish Over SSH` `Git` `Apache (httpd)` `Freestyle Project`
