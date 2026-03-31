# Day 71 - Challenge 
# ⚙️ Jenkins Automation: Install Packages on Storage Server

## 📜 Task Description
The Nautilus DevOps team required a Jenkins job to automate package installation on the Storage Server within the Stratos Datacenter. The job should accept a parameter and install the specified package remotely.

---

## 🚀 Solution Steps

### 1️⃣ Access Jenkins
- Click on the **Jenkins** button in the top bar.
- Login using:
  - **Username:** admin  
  - **Password:** Adm!n321  

---

### 2️⃣ Create Jenkins Job
- Click **New Item**
- Enter job name: `install-packages`
- Select **Freestyle Project**
- Click **OK**

---

### 3️⃣ Configure Parameter
- Enable **This project is parameterized**
- Click **Add Parameter → String Parameter**
- Set:
  - **Name:** PACKAGE  
  - **Default Value:** vim  
  - **Description:** Enter the package name to install on the storage server  

---

### 4️⃣ Generate SSH Key (Jenkins Server)
```bash
sudo su - jenkins
ssh-keygen -t rsa -b 2048 -N "" -f ~/.ssh/id_rsa
cat ~/.ssh/id_rsa.pub
Copy the public key.

5️⃣ Add SSH Key to Storage Server

Login to storage server:

ssh natasha@ststor01

Add key:

mkdir -p ~/.ssh
echo "YOUR_PUBLIC_KEY" >> ~/.ssh/authorized_keys
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
chmod 700 ~
exit
6️⃣ Verify Passwordless SSH

On Jenkins server:

ssh -o StrictHostKeyChecking=no natasha@ststor01 "hostname"

✅ Expected Output:

ststor01
7️⃣ Allow Passwordless Package Installation

On storage server:

echo "natasha ALL=(ALL) NOPASSWD: /usr/bin/yum" | sudo tee -a /etc/sudoers
8️⃣ Configure Build Step in Jenkins
Go to job → Configure
Scroll to Build Steps
Click Add build step → Execute shell
Add command:
ssh -o StrictHostKeyChecking=no natasha@ststor01 "sudo yum install -y $PACKAGE"
Click Save
9️⃣ Build the Job
Click Build with Parameters
Enter:
PACKAGE = vim-enhanced
Click Build
🔟 Verify Output
Open Console Output
Confirm:
Installed: vim-enhanced
Finished: SUCCESS
🔁 Test Reliability

Run job again with another package:

PACKAGE = wget

Ensure:

Finished: SUCCESS