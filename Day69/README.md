# Day 69 - Challenge 
# 🧩 Jenkins Plugin Installation Task (KodeKloud DevOps Lab)

## 📜 Task Description
The Nautilus DevOps team has recently set up a Jenkins server to use for various CI/CD jobs. Before creating and running jobs, they want to install some essential plugins that will be used across most builds.

Your task is to log in to the Jenkins UI and install the **Git** and **GitLab** plugins as described below.

---

## 🎯 Task Requirements

1️⃣ Click on the **Jenkins** button on the top bar to access the Jenkins UI.  
2️⃣ Log in using the following credentials:
   - **Username:** admin  
   - **Password:** Adm!n321  
3️⃣ Once logged in, install the following plugins:
   - **Git Plugin**
   - **GitLab Plugin**
4️⃣ If prompted, choose **“Restart Jenkins when installation is complete and no jobs are running”**.  
5️⃣ After Jenkins restarts, wait for the login page to reappear before proceeding.

---

## ✅ Solution Steps

1️⃣ **Access Jenkins Web UI**
- Click on the **Jenkins** button on the top navigation bar in the lab interface.  
- Log in using:
  - Username: `admin`  
  - Password: `Adm!n321`  

2️⃣ **Open Plugin Manager**
- From the Jenkins dashboard, navigate to:  
  **Manage Jenkins → Plugins → Available plugins**  
  (In some versions: **Manage Jenkins → Manage Plugins → Available** tab)

3️⃣ **Search and Install Plugins**
- In the search box, type **Git** → select the **Git Plugin** checkbox.  
- Then type **GitLab** → select the **GitLab Plugin** checkbox.  
- Click **Install without restart** (or **Download now and install after restart** if shown).

4️⃣ **Wait for Installation to Complete**
- Jenkins will start downloading and installing the selected plugins.  
- Once installation finishes, check the box **“Restart Jenkins when installation is complete and no jobs are running.”**

5️⃣ **Restart Jenkins**
- Jenkins will automatically restart itself.  
- Wait patiently until the **login page** reappears in your browser.

6️⃣ **Verify Plugin Installation**
- Log in again using your admin credentials (`admin / Adm!n321`).  
- Go to **Manage Jenkins → Plugins → Installed plugins**.  
- Confirm that both **Git** and **GitLab** plugins appear in the installed list.

---

## 🧠 Notes
- Restarting Jenkins is required after plugin installation to activate them properly.  
- If the Jenkins service hangs or UI does not reload, you can restart it manually from the terminal:  
  ```bash
  systemctl restart jenkins

