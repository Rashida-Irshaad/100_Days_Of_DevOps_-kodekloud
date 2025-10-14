# Day 68 - Challenge
# 🚀 Jenkins Installation and Configuration Task (KodeKloud DevOps Lab)

## 🧩 Task Description
The DevOps team at **xFusionCorp Industries** is initiating the setup of CI/CD pipelines and has decided to utilize **Jenkins** as their automation server. Your task is to perform the installation and configuration of Jenkins on the `jenkins` server as per the following requirements:

### 🔧 Requirements
1. Install Jenkins on the **jenkins server** using the **yum** utility only, and start its service.  
2. If you face a timeout issue while starting Jenkins, fix it accordingly.  
3. Use **Java 17** for Jenkins.  
4. Create an admin user during setup with the following details:  
   - **Username:** theadmin  
   - **Password:** Adm!n321  
   - **Full Name:** Rose  
   - **Email:** rose@jenkins.stratos.xfusioncorp.com  
5. Access the Jenkins UI from the **“Jenkins”** button on the top bar in the lab interface after installation and setup.

---

## ✅ Solution Steps

1️⃣ **Add Jenkins Repository and Import Key**  
`yum install -y wget`  
`wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo`  
`rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key`

2️⃣ **Install Java 17 and Jenkins**  
`yum install -y java-17-openjdk jenkins`

3️⃣ **Set Java 17 as the Default Java Version**  
`alternatives --set java /usr/lib/jvm/java-17-openjdk-*/bin/java`  
`java -version`  
✅ Ensure output shows Java 17 (e.g. `openjdk version "17..."`)

4️⃣ **Update Jenkins Systemd Service File (if needed)**  
If Jenkins fails to start or gives a timeout issue, fix its ExecStart path:  
`sed -i 's|^ExecStart=.*|ExecStart=/usr/bin/java -Djava.awt.headless=true -jar /usr/share/java/jenkins.war --httpPort=8080|' /usr/lib/systemd/system/jenkins.service`

5️⃣ **Reload Daemon and Start Jenkins Service**  
`systemctl daemon-reload`  
`systemctl enable jenkins`  
`systemctl start jenkins`  
`systemctl status jenkins -l --no-pager`  
✅ Confirm Jenkins shows: `Active: active (running)`

6️⃣ **Retrieve Jenkins Initial Admin Password**  
`cat /var/lib/jenkins/secrets/initialAdminPassword`  
Copy the displayed password to use during Jenkins UI setup.

7️⃣ **Access Jenkins UI and Create Admin User**  
- Click the **“Jenkins”** button on the lab’s top bar.  
- Enter the **initial admin password** from the previous step.  
- Choose **“Install suggested plugins”**.  
- Create the admin user with the exact details:  
  - Username: theadmin  
  - Password: Adm!n321  
  - Full Name: Rose  
  - Email: rose@jenkins.stratos.xfusioncorp.com  
- Click **Save and Continue → Start using Jenkins**.

8️⃣ **Verify Jenkins Service**  
`systemctl status jenkins | grep Active`  
✅ Output should confirm: `Active: active (running)`

---

## 🎯 Final Verification
- Jenkins installed via `yum`  
- Java 17 configured correctly  
- Jenkins service running successfully  
- Admin user `theadmin (Rose)` created with correct email  
- Jenkins accessible via the lab UI  

---

### 🏁 Task Completed Successfully
You have successfully installed and configured Jenkins with the required admin user and Java 17 runtime on the Jenkins server. 🎉

