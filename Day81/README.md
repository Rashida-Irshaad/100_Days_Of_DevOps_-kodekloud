# Day 81 - Challenge
 # KodeKloud Task: Jenkins Pipeline to Deploy Static Website

## Task Description

The development team of xFusionCorp Industries is developing a new static website and plans to deploy it on **Nautilus App Server** using a **Jenkins pipeline**.

**Access:**
- Jenkins UI: username `admin`, password `Adm!n321`
- Gitea UI: username `sarah`, password `Sarah_pass123`
- Repository `sarah/web` is already cloned on App Server 1 under `/var/www/html`

**Requirements:**
1. Update `index.html` in the repo to `Welcome to xFusionCorp Industries` and push to the `master` branch.
2. Apache is already installed and running on port `8080`.
3. Add App Server 1 as a Jenkins agent node:
   - Name: `App Server 1`
   - Label: `stapp01`
   - Remote root directory: `/home/sarah/jenkins_agent`
   - Launch via SSH, host `stapp01`, credentials for user `sarah`
   - Install `java-17-openjdk` on App Server 1 if needed
4. Create a Jenkins pipeline job named `deploy-job` (NOT Multibranch) with two stages (case sensitive): `Deploy` and `Test`.
   - `Deploy`: deploy code from the `web` repo to `/var/www/html` on App Server 1
   - Pipeline must run on the App Server 1 node (label `stapp01`)
   - `Test`: verify the site is reachable, e.g. `curl` against `http://stlb01:8091`. It must fail if the site is down or if `Deploy` fails.
5. Click the **App** button: the content must load at `http://stlb01:8091` with no sub-directory such as `/web`.

---

## Solution Steps

### PART 1: Update index.html on App Server 1

```bash
ssh tony@stapp01
sudo su - sarah
cd /var/www/html
echo "Welcome to xFusionCorp Industries" > index.html
git add index.html
git commit -m "Update index.html"
git push origin master
```

- Git username/password if asked: `sarah` / `Sarah_pass123`
- If you get a "dubious ownership" error:
  ```bash
  git config --global --add safe.directory /var/www/html
  ```
- Make sure `sarah` owns the web root:
  ```bash
  sudo chown -R sarah:sarah /var/www/html
  ```

### PART 2: Prepare App Server 1 (as tony)

```bash
sudo yum install -y java-17-openjdk
java -version
sudo mkdir -p /home/sarah/jenkins_agent
sudo chown -R sarah:sarah /home/sarah/jenkins_agent
echo "sarah:Sarah_pass123" | sudo chpasswd
```

Make sure SSH password login is enabled:

```bash
sudo sshd -T | grep -i passwordauthentication
# If "no":
sudo sed -i 's/^PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config
sudo systemctl restart sshd
```

Test from the jump host:

```bash
ssh sarah@stapp01
```

### PART 3: Install Jenkins plugins

1. Open Jenkins and log in (`admin` / `Adm!n321`).
2. **Manage Jenkins → Plugins → Available plugins**
3. Install if missing: **SSH Build Agents**, **Git**, **Pipeline**
4. Tick **Restart Jenkins when installation is complete and no jobs are running**
5. Refresh the page and log in again.

### PART 4: Add credentials for sarah

**Option A: Username with password**
1. **Manage Jenkins → Credentials → System → Global credentials → Add Credentials**
2. Kind: **Username with password**
3. Username: `sarah` (no trailing space)
4. Password: `Sarah_pass123`
5. ID: `sarah-ssh`
6. **Create**

**Option B: SSH key (use this if the password keeps failing)**

On stapp01 as `sarah`:

```bash
mkdir -p ~/.ssh && chmod 700 ~/.ssh
ssh-keygen -t rsa -b 4096 -N "" -f ~/.ssh/id_rsa
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
cat ~/.ssh/id_rsa
```

In Jenkins: add Credentials → Kind **SSH Username with private key** → ID `sarah-key` → Username `sarah` → Private Key **Enter directly** → paste the full key (with BEGIN/END lines) → **Create**.

### PART 5: Add App Server 1 as an agent

1. **Manage Jenkins → Nodes → New Node**
2. Name: `App Server 1`, select **Permanent Agent**, **Create**
3. Fill in:
   - Remote root directory: `/home/sarah/jenkins_agent`
   - Labels: `stapp01`
   - Launch method: **Launch agents via SSH**
   - Host: `stapp01`
   - Credentials: the `sarah` credential you created
   - Host Key Verification Strategy: **Non verifying Verification Strategy**
4. **Save**
5. Open the node log. It should end with **Agent successfully connected and online**. If not, click **Launch agent** again.

### PART 6: Create the pipeline job

1. **New Item** → name `deploy-job` → select **Pipeline** (not Multibranch) → **OK**
2. In the **Pipeline** section choose **Pipeline script** and paste:

```groovy
pipeline {
    agent {
        label 'stapp01'
    }
    stages {
        stage('Deploy') {
            steps {
                sh '''
                    cd /var/www/html
                    git pull origin master
                '''
            }
        }
        stage('Test') {
            steps {
                sh 'curl -f http://stlb01:8091'
            }
        }
    }
}
```

3. **Save**, then click **Build Now**.

### PART 7: Fix git pull problems (only if Deploy fails)

On App Server 1 as `sarah`:

```bash
cd /var/www/html
git config --global --add safe.directory /var/www/html
git config credential.helper store
git pull origin master
```

Enter `sarah` / `Sarah_pass123` once, then click **Build Now** again.

### PART 8: Verify

1. **Build #1 → Console Output**: both stages green, ending with **SUCCESS**.
2. Click the **App** button on the top bar. You should see `Welcome to xFusionCorp Industries` at `http://stlb01:8091` (no `/web`).

---

## Troubleshooting

| Problem | Fix |
|---|---|
| Agent offline, "Wrong password" | Re-enter the credential, check no trailing space in username, run `chpasswd`, confirm `PasswordAuthentication yes`, or switch to SSH key |
| Agent offline, Java error | `sudo yum install -y java-17-openjdk` on stapp01 |
| `Permission denied` on `/var/www/html` | `sudo chown -R sarah:sarah /var/www/html` |
| Test stage fails | Check Apache: `sudo systemctl status httpd` |
| Old content still showing | Confirm the push to `master` worked and Jenkins pulled it |

Take screenshots of the node status, job config, and successful build for review.
