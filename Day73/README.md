# Day 73 - Challenge 
# ⚙️ Jenkins Job: Copy Apache Logs to Storage Server

## 📜 Task Description
The DevOps team required Apache logs from App Server 1 for troubleshooting. A Jenkins job was created to periodically copy logs to the Storage Server.

---

## 🚀 Solution Steps

### 1️⃣ Access Jenkins
- Open Jenkins UI
- Login:
  - Username: admin  
  - Password: Adm!n321  

---

### 2️⃣ Create Job
- Click **New Item**
- Name: `copy-logs`
- Select **Freestyle Project**
- Click **OK**

---

### 3️⃣ Configure Build Trigger
- Enable **Build periodically**
- Add cron expression:
*/12 * * * *


---

### 4️⃣ Configure Build Step
- Add build step → Execute shell

```bash
ssh -o StrictHostKeyChecking=no tony@stapp01 "sudo cp /var/log/httpd/access_log /var/log/httpd/error_log /tmp && scp /tmp/access_log /tmp/error_log natasha@ststor01:/usr/src/sysops/"
5️⃣ Setup Passwordless SSH
Jenkins → App Server
sudo su - jenkins
ssh-keygen -t rsa -b 2048 -N "" -f ~/.ssh/id_rsa

Add key to stapp01:

mkdir -p ~/.ssh
echo "PUBLIC_KEY" >> ~/.ssh/authorized_keys
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
App Server → Storage Server
ssh-keygen -t rsa -b 2048 -N "" -f ~/.ssh/id_rsa

Add key to ststor01:

mkdir -p ~/.ssh
echo "PUBLIC_KEY" >> ~/.ssh/authorized_keys
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
6️⃣ Build the Job
Click Build Now
7️⃣ Verify Logs
ssh natasha@ststor01
ls /usr/src/sysops

Expected:

access_log
error_log
